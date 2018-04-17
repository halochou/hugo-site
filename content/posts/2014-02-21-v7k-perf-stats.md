---
date: "2014-02-21T14:00:00Z"
tags:
- ibm
- v7000
title: V7000性能数据收集机制
---

# V7000性能数据收集机制
V7000默认以5分钟间隔收集关于MDisks、VDisks及控制器节点的统计数据。
生成的文件位于`/dumps/iostats`，文件名形式`stats_type_stats_nodepanelname_date_time`。
例如：

    Nm_stats_000229_031123_072426
    Nv_stats_000229_031123_072426
    Nn_stats_000229_031123_072426

最大存储文件数量为16个，之后会删除最早的文件，写入最新的文件。
所以收集统计数据的原理就是在系统自动删除早期数据文件前将文件拷出。

## 修改采样间隔
`startstats`命令用于修改采样时间，早起版本固件中含有`stopstats`命令，现已废弃。即新版本V7000系统始终对性能数据进行采集，采集动作无法停止，只能使用`startstats`改变间隔。

例如将采样间隔改为25分钟：

	svctask startstats -interval 25

## 数据导出
`/dumps/iostats`中的数据可使用`scp`命令拷出，方法与Linux/Unix相同。

因为通过ssh仅可以访问当前生效的控制器节点（configuration node），如果希望拷出备用节点的数据，需使用`cpdumps`命令，该命令会将指定节点中的指定数据复制到`configuration node`，之后即可用scp拷出。

	svctask cpdumps -prefix /dumps/iostats nodeone

具体说明请参考[官方手册](http://pic.dhe.ibm.com/infocenter/svc/ic/topic/com.ibm.storage.svc.console.610.doc/svc_clustercomm_21ie7a.html)

# svcmon
`svcmon`是一套实用脚本工具，使用Perl开发，可以自动完成SVC/V7000的数据采集、制图工作。但该工具并非IBM官方出品，请谨慎使用。（IBM官方仅支持TPC）
[svcmon主页](https://www.ibm.com/developerworks/community/blogs/svcmon/entry/introduction?lang=en)
##svcmon实现原理
`svcmon`使用`scp`下载`/dumps/iostats`中的数据，使用`PostgreSQL`存储数据，原理上与手工收集相同。

收集动作源代码：

    ###############################################################################
    #
    # CopyGetIostatsList
    #
    # 1. Called by none-config nodes.
    # 2. Get stats file names on local node using the lsiostatsdumps command.
    # 2. Adding (unshift) stats file names that match front panel id ($front_panel_id) to @stats_files array.
    # 3. Sort stats file names by date.
    #
    ###############################################################################

    sub CopyGetIostatsList {

        # Scan all (Node, VDisk, MDisk, Drive) stats files
        while(defined($stats_file = shift(@stats_files))) {

            # Handle Node stats file
            if($stats_file =~ m/Nn/ && $stats_file gt $Nn_last[$node]) {
                $command = $ssh . " svctask cpdumps -prefix /dumps/iostats/" . $stats_file .  " " . $node;
                ExecCommand();
                $command = "$scp$stats_file $dir";
                ExecCommand();
                &InsertDataBase();
                $Nn_last[$node] = $stats_file;
            }

            # Handle VDisk stats file
            if($stats_file =~ m/Nv/ && $stats_file gt $Nv_last[$node]) {
                $command = $ssh . " svctask cpdumps -prefix /dumps/iostats/" . $stats_file .  " " . $node;
                ExecCommand();
                $command = "$scp$stats_file $dir";
                ExecCommand();
                &InsertDataBase();
                $Nv_last[$node] = $stats_file;
            }

            # Handle MDisk stats file
            if($stats_file =~ m/Nm/ && $stats_file gt $Nm_last[$node]) {
                $command = $ssh . " svctask cpdumps -prefix /dumps/iostats/" . $stats_file .  " " . $node;
                ExecCommand();
                $command = "$scp$stats_file $dir";
                ExecCommand();
                &InsertDataBase();
                $Nm_last[$node] = $stats_file;
            }

            # Handle Drive stats file
            if($stats_file =~ m/Nd/ && $stats_file gt $Nd_last[$node]) {
                $command = $ssh . " svctask cpdumps -prefix /dumps/iostats/" . $stats_file .  " " . $node;
                ExecCommand();
                $command = "$scp$stats_file $dir";
                ExecCommand();
                &InsertDataBase();
                $Nd_last[$node] = $stats_file;
            }
        }
    }


    ###############################################################################
    #
    # GetIostatsList
    #
    # 1. Called by config nodes.
    # 2. Get stats file names on local node using the lsiostatsdumps command.
    # 2. Adding (unshift) stats file names that match front panel id ($front_panel_id) to @stats_files array.
    # 3. Sort stats file names by date.
    #
    ###############################################################################

    sub GetIostatsList {

        # Scan all (Node, VDisk, MDisk, Drive) stats files
        while(defined($stats_file = shift(@stats_files))) {

            # Handle Node stats file
            if($stats_file =~ m/Nn/ && $stats_file gt $Nn_last[$node]) {
                $command = "$scp$stats_file $dir";
                ExecCommand();
                &InsertDataBase();
                $Nn_last[$node] = $stats_file;
            }

            # Handle VDisk stats file
            if($stats_file =~ m/Nv/ && $stats_file gt $Nv_last[$node]) {
                $command = "$scp$stats_file $dir";
                ExecCommand();
                &InsertDataBase();
                $Nv_last[$node] = $stats_file;
            }

            # Handle MDisk stats file
            if($stats_file =~ m/Nm/ && $stats_file gt $Nm_last[$node]) {
                $command = "$scp$stats_file $dir";
                ExecCommand();
                &InsertDataBase();
                $Nm_last[$node] = $stats_file;
            }

            # Handle Drive stats file
            if($stats_file =~ m/Nd/ && $stats_file gt $Nd_last[$node]) {
                $command = "$scp$stats_file $dir";
                ExecCommand();
                &InsertDataBase();
                $Nd_last[$node] = $stats_file;
            }
        }
    }

# V7000 ssh登录配置方法
1. [Setting up an SSH client on a Windows host](http://pic.dhe.ibm.com/infocenter/storwize/ic/index.jsp?topic=%2Fcom.ibm.storwize.v7000.710.doc%2Fsvc_sshonwindows.html)
2. [Preparing the SSH client on an AIX or Linux host](http://pic.dhe.ibm.com/infocenter/storwize/ic/index.jsp?topic=%2Fcom.ibm.storwize.v7000.710.doc%2Fsvc_sshonanaixhost_29elsk.html)
