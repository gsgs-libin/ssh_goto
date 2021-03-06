项目名称：goto

实现功能：远程登录服务器

构成：

1.收集服务器信息脚本--GET_HOST

2.goto实现脚本--GOTO

GET_HOST：

	收集服务器信息模块，目前只实现了自动收集aws服务器信息
	阿里云以及物理服务器的收集，只需要满足一定的格式要求存储就可以。
	脚本内容：

		#!/bin/bash
		region_list=('us-west-2' 'ap-south-1' 'ap-southeast-1' 'ap-northeast-1')
		for region in ${region_list[@]}
		do
			aws ec2 describe-instances --query 'Reservations[*].Instances[*].{region:Placement.AvailabilityZone, status:State.Name, instanceID:InstanceId, 		privateIP:PrivateIpAddress,publicIP:PublicIpAddress,instanceName:Tags[?Key==`Name`].Value}' --output json --region $region > ./jsonfile/$region.json
		done

	所在目录：

		AWS_COLLECTIONS
GOTO：

	实现列出各个区域的服务器信息
	实现模糊搜索匹配
	实现远程登录
	所在目录：

		SSH_GOTO

安装需求：

	1.tar xzvf SSH_GOTO.tar.gz -C ~ (下载到自己的用户目录，然后解压就可以)
	2.git clone git://github.com/seatgeek/fuzzywuzzy.git fuzzywuzzy && cd fuzzywuzzy && python setup.py install
	3.设置命令别名
		vim ~/.bashrc
		alias goto='python goto.py'
	4.里面有一个get_json_file.sh的文件，aws服务器信息统一会收集到新加坡ops机器，然后执行该脚本获取到自己的加目录
	  新加坡ops机器有定时任务在跑会定时更新aws服务器信息，所以当有服务器不在列表中，你可以手动执行该脚本进行获取。
	5.可能会有一个警告，需要安装python-Levenshtein

截图展示：

![image](https://github.com/gsgs-libin/ssh_goto/blob/master/ssh_goto_utils/img/choose_which_region.png)
![image](https://github.com/gsgs-libin/ssh_goto/blob/master/ssh_goto_utils/img/choose_which_agent.png)
