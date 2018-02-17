---
layout:     post
title:      "Xcode9命令行新打包方法"
subtitle:   "升级以后打包失败？"
date:       2017-9-21 4:00:00
author:     "Tom"
tags: [osx, ios]
category: [osx, ios]
comments: true
share: false
---
<h1>Xcode9命令行新打包方法</h1>

升级完Xcode9后，发现之前的打包命令出错了:

<pre>
xcodebuild[14404:541300] [MT] IDEDistribution: Step failed: &lt;IDEDistributionSigningAssetsStep: 0x7fe25dd32870&gt;: Error Domain=IDEDistributionSigningAssetStepErrorDomain Code=0 "Locating signing assets failed." UserInfo={NSLocalizedDescription=Locating signing assets failed., IDEDistributionSigningAssetStepUnderlyingErrors=(
"Error Domain=IDEProvisioningErrorDomain Code=9 ""test.app" requires a provisioning profile." UserInfo={NSLocalizedDescription="test.app" requires a provisioning profile., NSLocalizedRecoverySuggestion=Add a profile to the "provisioningProfiles" dictionary in your Export Options property list.}"
</pre>

经查，发现ExportOptions.plist对应的内容必须加上一个字段才可以正常工作:

<pre>
&lt;key&gt;provisioningProfiles&lt;/key&gt;
&lt;dict&gt;
	&lt;key&gt;com.your.bundleid&lt;/key&gt;
	&lt;string&gt;your provision description&lt;/string&gt;
&lt;/dict&gt;
</pre>
