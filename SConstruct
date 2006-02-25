# This file is used to compile grftool.
# You need scons: http://www.scons.org/
# Type 'scons -Q' in the commandline to compile grftool.

import os
import string

# Setup environment

USE_GTK = 0
WIN32 = 0

DEBUG = ARGUMENTS.get('DEBUG', 0);
CC = ARGUMENTS.get('CC', None);
CXX = ARGUMENTS.get('CXX', None);

PREFIX = ARGUMENTS.get('PREFIX', '/usr/local')
BINDIR = ARGUMENTS.get('BINDIR', PREFIX + '/bin')
DATADIR = ARGUMENTS.get('DATADIR', PREFIX + '/share')
PKGDATADIR = ARGUMENTS.get('PKGDATADIR', DATADIR + '/grftool')

platform = str(ARGUMENTS.get('OS', Platform()))
if platform == "cygwin" or platform == "windows":
	WIN32 = 1


env = Environment(
	CCFLAGS=Split('-Wformat -Wformat-security -Wparentheses -Wunused-variable -Wuninitialized -Wall -W -Wundef -Wpointer-arith -Wcast-align -Wno-unused-parameter -O2 -g'), # -Wconversion -Wcast-qual
	CPPPATH=['.'],
	LINKFLAGS='')
if CC != None:
	env['CC'] = CC
if CXX != None:
	env['CXX'] = CXX

if platform == "cygwin":
	env['CCFLAGS'] += ' -mno-cygwin'
	env['LINKFLAGS'] += ' -mno-cygwin'

env.SourceSignatures('timestamp')


# Check for GTK if we're not on cygwin

def CheckGtk(context, version):
	context.Message('Checking for GTK+ >= ' + version + '... ')
	pipes = os.popen3('pkg-config --modversion gtk+-2.0')
	out = pipes[1].read().rstrip("\n")
	pipes[0].close()
	pipes[2].close()
	if out <> '':
		context.Result(out)
	else:
		context.Result('not found')

	if out >= version:
		return 1
	else:
		return 0

if platform != "cygwin":
	conf = env.Configure(custom_tests = {'CheckGtk': CheckGtk})
	USE_GTK = 1
	if not conf.CheckGtk('2.2.0'):
		if ARGUMENTS.get('gtk', 0):
			print "*** Stop"
			Exit(1)
		else:
			print 'GTK frontend will be disabled.'
		USE_GTK = 0
	env = conf.Finish()


# Run sub SConscripts

Export('env WIN32 platform PREFIX BINDIR DATADIR PKGDATADIR DEBUG')
SConscript('lib/SConscript', build_dir='lib/static', duplicate=False)

# We only build the DLL if we're on Win32
if WIN32:
	SConscript('lib/zlib/SConscript', duplicate=False)
	SConscript('lib/SConscript-dll', build_dir='lib/dll', duplicate=False)

SConscript('tools/SConscript')

if USE_GTK:
	SConscript('gtk/SConscript')


env.Alias('install', [BINDIR, DATADIR + '/applications', PKGDATADIR])
