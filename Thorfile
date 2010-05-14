###########################################
#
#  Backup utils for cassandra
#

# [Use cases]
#
# 1. dump all sstables into specified directory
#
#      % thor backup:dump Keyspace1 20100514
#
#    or simple call task without arguments.
#
#      % thor backup:dump
#
#    This shows usage.
#
# 2. restore sstables from specified directory
#
#      % thor backup:restore Keyspace1 20100514
#
#    [NOTE] Not Implemented Yet :)
#
# 
# Author : maina@wota.jp
# License: MIT
#

require 'pathname'

class Backup < Thor
  namespace :backup
  default_task :dump

  desc "dump <KEYSPACE> <BACKUP_DIR>", "dump sstable into json"
  def dump(keyspace = "Keyspace1", dir = nil)
    unless dir
      dryrun = true
      dir = Time.now.strftime("%Y%m%d")
      puts <<-USAGE
Argument Error: needs both <KEYSPACE> and <BACKUP_DIR> arguments.

For Example:

    thor backup:dump #{keyspace} #{dir}

This will execute following commands.
Try again like that, or simply copy and paste.

      USAGE
    end

    sstables_for(keyspace).each do |sstable|
      sstable2json(sstable, dir, dryrun)
    end
  end

  private
    def sstables_for(keyspace)
      (dir = Pathname("/var/lib/cassandra/data/#{keyspace}")).directory? or
        raise "Directory not found: #{dir}"
      (files = Dir.glob(dir + "*-Data.db")).empty? and
	raise "File not found: *-Data.db (#{dir})"
      return files
    end

    def sstable2json(src, dir, dryrun = false)
      dir     = Pathname(dir)
      json    = dir + "#{File.basename(src)}.json"
      command = "sstable2json #{src} > #{json}"

      if dryrun
        puts command
      else
        dir.mkpath
        system command
      end
    end
end
