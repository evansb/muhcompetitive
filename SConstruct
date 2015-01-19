
import os
import config
import shutil

AddOption('--create',
          dest='NEW_PROBLEM',
          type='string',
          nargs=1,
          action='store',
          metavar='NAME',
          help='start doing a problem')

QUAL_BIN_DIR = os.path.join(os.getcwd(), config.BIN_DIR)
QUAL_INPUT_DIR = os.path.join(os.getcwd(), config.INPUT_DIR)
QUAL_OUTPUT_DIR = os.path.join(os.getcwd(), config.OUTPUT_DIR)
QUAL_EXPECTED_DIR = os.path.join(os.getcwd(), config.EXPECTED_DIR)

class Problem:

  def __init__(self, category, name, path):
    self.category = category
    self.name = name
    self.path = path
    self.qualname = category + '/' + name

  def exe_path(self):
    return os.path.join(QUAL_BIN_DIR, self.qualname)

  def input_path(self):
    return os.path.join(QUAL_INPUT_DIR, self.qualname)

  def output_path(self):
    return os.path.join(QUAL_OUTPUT_DIR, self.qualname)

  def expected_path(self):
    return os.path.join(QUAL_EXPECTED_DIR, self.qualname)

  def build(self, env):
    out = self.output_path()
    exe = self.exe_path()
    env.Program(target=exe, source=[ self.path ])
    test = env.Command(\
        target = out,
        source = exe,
        action = [ exe + " < " + self.input_path() + " > " + out,
                   "diff " + out + " " + self.expected_path() ])
    env.Alias(self.qualname, test)

def get_all_cpp_files(dir):

  def filter_not_cpp(dir):
    return filter(lambda d: '.cpp' in d and d[0] not in config.IGNORE, dir)

  def filter_ignored_dir(dir):
    return filter(lambda d: d[0] not in config.IGNORE, dir)

  cpp = set()
  for dirname, dirnames, filenames in os.walk(dir):
    dirnames = filter_ignored_dir(dirnames)
    filenames = filter_not_cpp(filenames)
    for subdirname in dirnames:
      path = os.path.join(dirname, subdirname)
      cpp.union(get_all_cpp_files(path))
    for filename in filenames:
      path = os.path.join(dirname, filename)
      cpp.add((os.path.basename(dirname), filename[:-4], path))
  return cpp

def make_file_if_not_exists(path):
  dir = os.path.dirname(path)
  if not os.path.exists(dir):
    os.makedirs(dir)
  if not os.path.exists(path):
    with open(path, "w"):
      pass

env = Environment(NEW_PROBLEM = GetOption('NEW_PROBLEM'))
env.Append(CC=config.CC)
env.Append(CCFLAGS=config.CCFLAGS)

if env["NEW_PROBLEM"]:
  NEW_PROBLEM = env["NEW_PROBLEM"]
  category, name = NEW_PROBLEM.split("/")
  prob = Problem(category, name,\
      os.path.join(os.getcwd(), NEW_PROBLEM) + ".cpp")
  map(make_file_if_not_exists,\
      [ prob.input_path(), prob.output_path(),\
        prob.expected_path(), prob.path ])
  shutil.copy2(os.path.join(os.getcwd(), config.TEMPLATE), prob.path)
  prob.build(env)
else:
  for dirname, filename, path in get_all_cpp_files(os.getcwd()):
    Problem(dirname, filename, path).build(env)
