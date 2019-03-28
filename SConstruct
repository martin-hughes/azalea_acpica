# SConstruct file for the Azalea port of ACPICA
#
# I've chosen to use this because I'm more familiar with it than regular Makefiles, and I want to be able to move
# quickly. Once the project is more mature perhaps I will fall back on the old Makefile.

import os

def main_build_script(vars):
  global dependencies
  lib_env = build_default_env()

  sys_image_root = vars["sys_image_root"]
  kernel_include_dir = os.path.join(sys_image_root, "apps", "developer", "kernel", "include")
  libc_include_dir = os.path.join(sys_image_root, "apps", "developer", "libc", "include")

  lib_install_dir = os.path.join(sys_image_root, "apps", "developer", "acpica", "lib")
  header_install_dir = os.path.join(sys_image_root, "apps", "developer", "acpica", "include")

  lib_env['CXXFLAGS'] = '-mno-red-zone -nostdlib -nostdinc -nodefaultlibs -mcmodel=large -ffreestanding -fno-exceptions -std=c++17 -U _LINUX -U __linux__ -D __AZALEA__ -Wno-bitwise-op-parentheses -Wno-shift-op-parentheses'
  lib_env['CFLAGS'] = '-mno-red-zone -nostdlib -nostdinc -nodefaultlibs -mcmodel=large -ffreestanding -fno-exceptions -U _LINUX -U __linux__ -D __AZALEA__ -Wno-bitwise-op-parentheses -Wno-shift-op-parentheses'
  lib_env.AppendENVPath('CPATH', '#/source/include')
  lib_env.AppendENVPath('CPATH', '#/source/compiler')
  lib_env.AppendENVPath('CPATH', kernel_include_dir)
  lib_env.AppendENVPath('CPATH', libc_include_dir)

  lib_obj = lib_env.SConscript("#SConscript", 'lib_env', variant_dir='output', duplicate=0)
  lib_env.Install(lib_install_dir, lib_obj)
  lib_env.Alias('install', lib_install_dir)

  headers = lib_env.File(Glob("source/include/*.h"))
  plat_headers = lib_env.File(Glob("source/include/platform/*.h"))
  plat_include_folder = os.path.join(header_install_dir, "platform")
  lib_env.Install(header_install_dir, headers)
  lib_env.Install(plat_include_folder, plat_headers)
  lib_env.Alias('install', header_install_dir)
  lib_env.Alias('install', plat_include_folder)

def build_default_env():
  env = Environment()
  env['CC'] = 'clang'
  env['CXX'] = 'clang++'
  env['CPPPATH'] = '#'
  env['LINK'] = 'g++'
  env['AS'] = 'nasm'
  env['ASFLAGS'] = '-f elf64'
  env['RANLIBCOM'] = ''
  env['ASCOMSTR'] =   "Assembling:   $TARGET"
  env['CCCOMSTR'] =   "Compiling:    $TARGET"
  env['CXXCOMSTR'] =  "Compiling:    $TARGET"
  env['LINKCOMSTR'] = "Linking:      $TARGET"
  env['ARCOMSTR'] =   "(pre-linking) $TARGET"
  env['LIBS'] = [ ]

  return env

def construct_variables():
  var = Variables(["azalea_config/default_config.py", "variables.cache" ], ARGUMENTS)
  var.AddVariables(
    PathVariable("sys_image_root",
                 "Root of Azalea system image. Look for include files, libraries and installation locations here.",
                 None,
                 PathVariable.PathIsDir))

  e = Environment(variables = var)

  var.Save("variables.cache", e)

  return e

vars = construct_variables()


main_build_script(vars)