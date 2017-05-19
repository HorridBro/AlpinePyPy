diff -r 400ffc527c25 lib_pypy/_tkinter/tklib_build.py
--- a/lib_pypy/_tkinter/tklib_build.py	Thu May 11 10:39:45 2017 +0000
+++ b/lib_pypy/_tkinter/tklib_build.py	Fri May 12 13:26:52 2017 +0300
@@ -22,12 +22,23 @@
     linklibs = ['tcl', 'tk']
     libdirs = []
 else:
+    # On some Linux distributions, the tcl and tk libraries are
+    # stored in /usr/include, so we must check this case also
+    found = False
     for _ver in ['', '8.6', '8.5', '']:
         incdirs = ['/usr/include/tcl' + _ver]
         linklibs = ['tcl' + _ver, 'tk' + _ver]
         libdirs = []
         if os.path.isdir(incdirs[0]):
+            found = True
             break
+    if not found:
+        for _ver in ['8.6', '8.5', '']:
+            incdirs = ['/usr/include']
+            linklibs = ['tcl' + _ver, 'tk' + _ver]
+            libdirs=[]
+            if os.path.isfile(''.join([ '/usr/lib/lib', linklibs[1], '.so'])):
+                break
 
 config_ffi = FFI()
 config_ffi.cdef("""
diff -r 400ffc527c25 pypy/goal/targetpypystandalone.py
--- a/pypy/goal/targetpypystandalone.py	Thu May 11 10:39:45 2017 +0000
+++ b/pypy/goal/targetpypystandalone.py	Fri May 12 13:26:52 2017 +0300
@@ -269,6 +269,9 @@
                 raise Exception("Cannot use the --profopt option "
                                 "when --shared is on (it is by default). "
                                 "See issue #2398.")
+        elif config.translation.nopax is not None:
+            raise Exception("Cannot use the --nopax option "
+                            "when --shared is off (it is on by default). ")
         if sys.platform == 'win32':
             libdir = thisdir.join('..', '..', 'libs')
             libdir.ensure(dir=1)
diff -r 400ffc527c25 rpython/config/translationoption.py
--- a/rpython/config/translationoption.py	Thu May 11 10:39:45 2017 +0000
+++ b/rpython/config/translationoption.py	Fri May 12 13:26:52 2017 +0300
@@ -146,6 +146,9 @@
               cmdline="--profopt"),
     BoolOption("noprofopt", "Don't use profile based optimization",
                default=False, cmdline="--no-profopt", negation=False),
+    BoolOption("nopax", "Use this in case your system comes with a PAX protection. --nopax will disable it for pypy, so that it can use the jit. Requires paxmark to be installed",
+               default=False,
+               cmdline="--nopax"),
     BoolOption("instrument", "internal: turn instrumentation on",
                default=False, cmdline=None),
     BoolOption("countmallocs", "Count mallocs and frees", default=False,
diff -r 400ffc527c25 rpython/translator/c/genc.py
--- a/rpython/translator/c/genc.py	Thu May 11 10:39:45 2017 +0000
+++ b/rpython/translator/c/genc.py	Fri May 12 13:26:52 2017 +0300
@@ -391,6 +391,8 @@
         shared = self.config.translation.shared
 
         extra_opts = []
+        if self.config.translation.nopax:
+            extra_opts += ["nopax"]
         if self.config.translation.make_jobs != 1:
             extra_opts += ['-j', str(self.config.translation.make_jobs)]
         if self.config.translation.lldebug:
@@ -442,6 +444,21 @@
                 'cd $(RPYDIR)/translator/goal && $(ABS_TARGET) $(PROFOPT)',
                 '$(MAKE) clean_noprof',
                 '$(MAKE) CFLAGS="-fprofile-use $(CFLAGS)" LDFLAGS="-fprofile-use $(LDFLAGS)" $(TARGET)']))
+
+        # No-pax code for the make file
+
+
+
+        if self.config.translation.nopax:
+            mk.definition('PAX_TARGET','pypy-c')
+            # mk.rule('$(PAX_TARGET)','$(TARGET) main.o','paxmark -zm $(PAX_TARGET)')
+
+            rules.append(('$(PAX_TARGET)','$(TARGET) main.o',[
+                '$(CC_LINK) $(LDFLAGS_LINK) main.o -L. -l$(SHARED_IMPORT_LIB) -o $@ $(RPATH_FLAGS)',
+                'paxmark -zm $(PAX_TARGET)']))
+
+            mk.rule('nopax','',
+                    '$(MAKE) CFLAGS="$(CFLAGS) $(CFLAGSEXTRA)" LDFLAGS="$(LDFLAGS)" $(PAX_TARGET)')
         for rule in rules:
             mk.rule(*rule)
 
