dnl  configuration script for rlwrap
dnl  Process this file with autoconf to produce configure.
dnl
dnl  Copyright (C) 2000-2007 Hans Lub hlub@knoware.nl
dnl
dnl  This file is part of rlwrap
dnl
dnl  rlwrap is free software; you can redistribute it and/or modify it
dnl  under the terms of the GNU General Public License as published by
dnl  the Free Software Foundation; either version 2, or (at your
dnl  option) any later version.
dnl
dnl  rlwrap is distributed in the hope that it will be useful, but
dnl  WITHOUT ANY WARRANTY; without even the implied warranty of
dnl  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
dnl  General Public License for more details.
dnl
dnl  You should have received a copy of the GNU General Public License
dnl  along with GUILE; see the file COPYING.  If not, write to the
dnl  Free Software Foundation, Inc., 59 Temple Place - Suite 330,
dnl  Boston, MA 02111-1307, USA.




AC_INIT

AC_PREREQ(2.61)



AC_CONFIG_AUX_DIR(tools)
AC_CANONICAL_HOST
AC_CONFIG_SRCDIR(src/main.c)
AM_INIT_AUTOMAKE(rlwrap,0.37)

AC_PROG_INSTALL
AM_CONFIG_HEADER([config.h])
AC_PROG_MAKE_SET
AM_SANITY_CHECK


# option parsing for optional features
opt_debug=no
opt_homegrown_redisplay=no
opt_spy_on_readline=yes

AC_ARG_ENABLE(debug,
    AC_HELP_STRING([--enable-debug], [enable debugging [[default=NO]]]),
    opt_debug=$enableval)

AC_ARG_ENABLE(homegrown-redisplay,
    AC_HELP_STRING([--enable-homegrown-redisplay], [try this only if rlwrap messes up the prompt [[default=NO]]]),
    opt_homegrown_redisplay=$enableval)

AC_ARG_ENABLE(spy-on-readline,
    AC_HELP_STRING([--enable-spy-on-readline [[default=YES]]],
	[use private readline variable to keep screen tidy when resizing terminal [[default=YES]]]),
    opt_spy_on_readline=$enableval)


if test $opt_debug = yes; then
    AC_DEFINE(DEBUG,1 ,Define to 1 to get debugging info)
    test -z "$CFLAGS" || AC_MSG_WARN([Because CFLAGS is set, configure will not add -g and -Wall to them - do it yourself if necessary])
    test -z "$CFLAGS" && AC_SUBST(CFLAGS,"-O0 -g -pedantic -Wall") # compile for debugging, and  give plenty of warnings


fi

AC_USE_SYSTEM_EXTENSIONS

test $opt_homegrown_redisplay = yes && AC_DEFINE(HOMEGROWN_REDISPLAY,1 ,[Define to 1 to use homegrown_redisplay()])

test $opt_spy_on_readline = yes && AC_DEFINE(SPY_ON_READLINE,1 ,Define to 1 to use private _rl_horizontal_scroll_mode)



AC_CONFIG_FILES([Makefile filters/Makefile doc/Makefile src/Makefile doc/rlwrap.man]) 

# Checks for programs.


AC_PROG_CC
AC_PROG_CPP

AC_PATH_PROG(PERL,perl)

AC_CHECK_PROG(STRIP,strip,strip,true)

# Checks for header files.
AC_HEADER_STDC
AC_HEADER_SYS_WAIT
AC_CHECK_HEADERS([errno.h fcntl.h  libutil.h stdlib.h string.h sched.h sys/ioctl.h sys/wait.h sys/resource.h stddef.h ])
AC_CHECK_HEADERS([termios.h unistd.h stdint.h time.h getopt.h regex.h curses.h termcap.h ])

AC_CHECK_HEADERS([ term.h  ncurses/term.h], , ,
    [#ifdef HAVE_CURSES_H
     #include <curses.h>
     #endif])



# Found this in configure.ac for 'top':
# The third argument to tputs is a putc-like function that takes an
# argument.  On most systems that argument is an int, but on some (you
# know who you are, Solaris!) it is a char.  Determine which:

_savedwerror_flag=$ac_c_werror_flag 
ac_c_werror_flag=yes # make $CC fail after warning
AC_MSG_CHECKING([argument type of tputs putc function])
AC_COMPILE_IFELSE( [AC_LANG_PROGRAM(
             [ #ifdef HAVE_CURSES_H
               #  include <curses.h>
               #  ifdef HAVE_TERM_H
               #    include <term.h>
               #  else 
               #    ifdef HAVE_NCURSES_TERM_H /* cygwin? AIX? */
               #      include <ncurses/term.h>
               #    endif
               #  endif
               #else
               #  ifdef HAVE_TERMCAP_H
               #     include <termcap.h>
               #  endif
               #endif
               int f(char i) { }],
             [tputs("a", 1, f);])],
             [ac_cv_type_tputs_putc="char"],
             [ac_cv_type_tputs_putc="int"])
AC_MSG_RESULT($ac_cv_type_tputs_putc)
AC_DEFINE_UNQUOTED(TPUTS_PUTC_ARGTYPE, $ac_cv_type_tputs_putc,
                   [Define as the type for the argument to the putc function of tputs ('int' or 'char')])
ac_c_werror_flag=$_savedwerror_flag       

AC_MSG_CHECKING([whether your getopt() correctly understands double colons in option string]) 
AC_RUN_IFELSE(
    [AC_LANG_PROGRAM( [[ const char optstring[] = "+:a::b";
		         char  * myargv [] = {"progname", "-a", "-b"}; 
		         int myargc = 3;
			 extern char *optarg;
		      ]] , [[
		         int opt = getopt(myargc, myargv, optstring);
		         if (opt != 'a' || optarg) /* optarg != NULL means that -b is seen as argument of -a */ 
		           exit (-1);
		        else
		           exit(0); ]] )],
    [AC_DEFINE(GETOPT_GROKS_OPTIONAL_ARGS, 1, Define if your getopt() correctly understands double colons in the option string)
     AC_SUBST(HAVE_OPTIONAL_ARGS,yes)
     ac_double_colons="yes"],
    [AC_SUBST(HAVE_OPTIONAL_ARGS,no)
     ac_double_colons="no"])
AC_MSG_RESULT($ac_double_colons)

# Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_TYPE_PID_T
AC_HEADER_TIME

# Checks for library functions.
AC_PROG_GCC_TRADITIONAL
AC_TYPE_SIGNAL

# Let groff -Tman know whether we have long options
AC_CHECK_FUNC([getopt_long], [AC_SUBST(HAVE_LONG_OPTS,[yes])],[AC_SUBST(HAVE_LONG_OPTS,[no])])

AC_CHECK_FUNCS(getopt_long isastream  mkstemp pselect putenv readlink sched_yield )
AC_CHECK_FUNCS(setenv setitimer setsid setrlimit sigaction snprintf strlcpy strlcat strnlen system)

# Try to determine the kind of pty support on this system
# This is not so easy and may guess wrong; if this happens:
#    - edit config.h by hand (and keep a copy of it somewhere as configure will overwrite it), and 
#    - *please* notify the author! (hanslub42@gmail.com) 
# Most of the code below is taken from rxvt-2.7.10 (Copyright (c) 1999-2001
# Geoff Wing <gcw@pobox.com>)

# In the newest FreeBSD, we need openpty, which exists in libutil
# NB: The rxvt configure script warns against this. Why?


AC_CHECK_LIB(util, openpty)
AC_CHECK_FUNCS(openpty getpty grantpt unlockpt)



AC_CACHE_CHECK(for getpt, ptyttylib_cv_func_getpt,
[AC_TRY_LINK([#define _GNU_SOURCE
#ifdef HAVE_STDLIB_H
# include <stdlib.h>
#endif],
  [(void)getpt();],
  ptyttylib_cv_func_getpt=yes, ptyttylib_cv_func_getpt=no)])
if test x$ptyttylib_cv_func_getpt = xyes; then
  AC_DEFINE(HAVE_GETPT, 1, Define if you have _GNU_SOURCE getpt() )
fi


AC_CACHE_CHECK(for pty/tty type, ptyttylib_cv_ptys,
[if test x$ac_cv_func_openpty = xyes; then
    ptyttylib_cv_ptys=OPENPTY
    AC_CHECK_HEADERS([pty.h])    
 else if test x$ac_cv_func__getpty = xyes; then
    ptyttylib_cv_ptys=SGI4
 else if test -c /dev/ttyp20; then
    ptyttylib_cv_ptys=SCO
 else if test -c /dev/ptym/clone; then
    ptyttylib_cv_ptys=HPUX
 else if test x$ptyttylib_cv_func_getpt = xyes; then
    ptyttylib_cv_ptys=GLIBC
 else if test -c /dev/ptc -a -c /dev/pts; then
    ptyttylib_cv_ptys=PTC
 else if test -c /dev/ptc -a -d /dev/pts; then
    ptyttylib_cv_ptys=PTC
 else if test -c /dev/ptmx -a -c /dev/pts/0; then
    ptyttylib_cv_ptys=STREAMS
 else case "$host_os" in
         *cygwin*) ptyttylib_cv_ptys=STREAMS ;;
         *)        ptyttylib_cv_ptys=PREHISTORIC ;;
      esac
fi
fi
fi
fi
fi
fi
fi
fi
])

if test x$ptyttylib_cv_ptys = xOPENPTY; then
  AC_DEFINE(PTYS_ARE_OPENPTY, 1, Define for this pty type)
fi
if test x$ptyttylib_cv_ptys = xSCO; then
  AC_DEFINE(PTYS_ARE_NUMERIC, 1, Define for this pty type)
fi
if test x$ptyttylib_cv_ptys = xSTREAMS; then
  AC_DEFINE(PTYS_ARE_PTMX, 1, Define for this pty type)
fi
if test x$ptyttylib_cv_ptys = xPTC; then
  AC_DEFINE(PTYS_ARE_PTC, 1, Define for this pty type)
fi
if test x$ptyttylib_cv_ptys = xSGI4; then
  AC_DEFINE(PTYS_ARE__GETPTY, 1, Define for this pty type)
fi
if test x$ptyttylib_cv_ptys = xCONVEX; then
  AC_DEFINE(PTYS_ARE_GETPTY, 1, Define for this pty type)
fi
if test x$ptyttylib_cv_ptys = xGLIBC; then
  AC_DEFINE(PTYS_ARE_GETPT, 1, Define for this pty type)
fi
if test x$ptyttylib_cv_ptys = xHPUX; then
  AC_DEFINE(PTYS_ARE_CLONE, 1, Define for this pty type)
fi
if test x$ptyttylib_cv_ptys = xPREHISTORIC -o x$ptyttylib_cv_ptys = xHPUX -o x$ptyttylib_cv_ptys = xGLIBC; then
  AC_DEFINE(PTYS_ARE_SEARCHED, 1, Define for this pty type)
fi


AC_CHECKING(for pty ranges)
ptys=`echo /dev/pty??`
pch1=`echo $ptys | tr ' ' '\012' | sed -e 's/^.*\(.\).$/\1/g' | sort -u | tr -d '\012'`
pch2=`echo $ptys | tr ' ' '\012' | sed -e 's/^.*\(.\)$/\1/g' | sort -u | tr -d '\012'`
if test x$pch1 != x; then
  AC_DEFINE_UNQUOTED(PTYCHAR1, "$pch1", Define for first char in devptyXX)
fi
if test x$pch2 != x; then
  AC_DEFINE_UNQUOTED(PTYCHAR2, "$pch2", Define for second char in devptyXX)
fi



# Checks whether readline needs additional libs.


AC_CHECK_FUNC(tgetent,
   [READLINE_SUPPLIB=],
   [AC_CHECK_LIB(curses, tgetent,
      [READLINE_SUPPLIB=-lcurses],
      [AC_CHECK_LIB(ncurses, tgetent,
         [READLINE_SUPPLIB=-lncurses],
         [AC_CHECK_LIB(termcap, tgetent,
            [READLINE_SUPPLIB=-ltermcap],
            [AC_MSG_WARN(No termcap nor curses library found)])
         ])
      ])
   ])


AC_CHECK_LIB(readline,readline,[READLINELIB=-lreadline],
	AC_MSG_ERROR([

You need the GNU readline library(ftp://ftp.gnu.org/gnu/readline/ ) to build
this program!

]), [$READLINE_SUPPLIB])

LIBS="$LIBS $READLINELIB $READLINE_SUPPLIB"

AC_MSG_CHECKING(whether your readline headers know about rl_set_screen_size)
AC_EGREP_HEADER([rl_set_screen_size],[readline/readline.h], true , 
	AC_MSG_ERROR([
You need a more recent version (at least 4.2) of the readline 
library (ftp://ftp.gnu.org/gnu/readline/) to build this program!
])) 
AC_MSG_RESULT(yes)

AC_MSG_CHECKING(whether your readline library knows about rl_set_screen_size)
AC_CHECK_FUNC(rl_set_screen_size,true,AC_MSG_ERROR([[
Although you have recent enough readline headers, the readline library that I found is old and/or incomplete.
This may e.g. happen on Mac OS X machines, where the readline library is just a re-badged copy of editline
Please re-configure with ./configure LDFLAGS='-L/path/to/the/real/readline/lib/'
]]))



AC_MSG_CHECKING(whether your readline knows about rl_variable_value)
AC_EGREP_HEADER([rl_variable_value],[readline/readline.h], [
                 AC_DEFINE(HAVE_RL_VARIABLE_VALUE, 1 ,[Define to 1 if your readline lib has rl_variable_value()])])
AC_MSG_RESULT(yes)

AC_MSG_CHECKING(whether your readline knows about rl_readline_version)
AC_EGREP_HEADER([rl_readline_version],[readline/readline.h], [
                 AC_DEFINE(HAVE_RL_READLINE_VERSION, 1 ,[Define to 1 if your readline lib has rl_readline_version])])
AC_MSG_RESULT(yes)



# check for existence of myself under /proc/$$/cwd
echo "Will rlwrap find command's working directory under /proc/<commands pid>/cwd? let's see..."
AC_CHECK_FILES(/proc/$$/cwd/configure.ac,[
                  AC_DEFINE(HAVE_PROC_PID_CWD,1 ,[Define to 1 if /proc/<pid>/cwd is a link to working directory of process <pid>])
                  AC_SUBST(HAVE_PROC_PID_CWD,[yes])
               ],[AC_SUBST(HAVE_PROC_PID_CWD,[no])])


echo
echo

#echo "please ignore any WARNINGs about ignoring --datarootdir below :-)"
#echo
#echo


AC_OUTPUT

cat <<EOF 

Now do:
    make (or gmake)  to build rlwrap 
    make check       for instructions how to test it
    make install     to install it
 
EOF


# Local Variables:
# mode: shell-script
# End:

