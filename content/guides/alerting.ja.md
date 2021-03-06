---
last_modified: 2015/04/02
translation_status: complete
language: ja
title: アラートの設定方法
sidebar:
  nav:
    - header: アラート設定のガイド
    - text: アラートの新規設定
      href: "#new"
    - text: メトリクスの指定
      href: "#metric"
    - text: メトリクスの変化量によるアラート
      href: "#change"
    - text: Simple Alert 又は Multi Alert
      href: "#simple"
    - text: アラート条件の設定
      href: "#conditions"
    - text: メトリクスが届かなくなった時の通知
      href: "#nodata"
    - text: アラートの自動解除
      href: "#autoresolve"
    - text: アラートメッセージの設定
      href: "#notify"
    - text: アラート送信先の設定
      href: "#notify1"
---

***注:*** 本ガイドは英文オリジナルの翻訳公開後、1年以上が経過しています。
本ガイドは新しいMonitor(監視)機能ガイド入門編に統合されました。
[Monitor(監視)機能の設定ガイド](/ja/guides/monitoring) ページを参照して下さい。

<!-- <p>Monitoring all of your infrastructure in one place wouldn't be complete without
the ability to know when critical changes are occurring.
Alerts within Datadog notify you when a metric crosses a threshold; they're flexible,
appear in the event stream, and can be delivered to team members of your choice via email,
Hipchat room, or whoever is 'oncall' in Pagerduty.
To set up an alert, go to the 'Metrics' tab and select 'Manage Alerts'.</p>

{{< img src="alert_1.png" >}}

<p>Two subtabs will appear, 'Triggered Alerts' and 'Manage Metric Alerts'.
'Triggered Alerts' are all alerts currently firing. Select 'Manage Metric Alerts' to
create a new alert.</p>

{{< img src="alert_2.png" >}} -->

運用しているシステム全体を一箇所で監視する場合、危機的な状況を起こしかねない状態の変化を検知できる機能なしでは完全とは言えません。

Datadog内のアラートは、メトリクスがしきい値を超えた時、ユーザに通知する機能を持っています。その通知は、自由度が高く、Dashboardのイベントストリームに表示され、電子メール、Hipchat部屋、またはPagerdutyの「ONCALL」経由で、該当のチームメンバに連絡をすることができます。

アラートを設定するには、`Metrics` タブに移動し、`Manage Monitors`を選択します。

{{< img src="ja-specific/alert_1.png" >}}

`Triggered Monitors` と`Manage Monitors` という補助的なタブが表示されます。
`Triggered Monitors` には、発生したアラートのリストが表示されています。

{{< img src="ja-specific/alert_2.png" >}}

新しくアラートを設定する場合は、`Manage Monitors` を選択します。


<!-- <h3 id="new">Creating a new alert</h3>
{{< img src="alert_3.png" >}}

<p>There are 5 steps to creating an alert</p>
<ol>
<li>Choose the average, minimum, maximum, or sum of a  metric over specific hosts,
tags, regions, devices, etc.</li>
<li>Choose a simple or multi alert; <a href="#simple">multi-alerts</a> are flexible
and can be across host, device, role, etc.</li>
<li>Set the alert conditions; if the metric was above or below a threshold value on average,
at least once, or at all times during a time span and to be alerted if the metric
is receiving no data.</li>
<li>Say what's happening in the alert and @ notify team members.</li>
<li>You can also notify specific services.</li>
</ol> -->


<h3 id="new">アラートの新規設定</h3>

{{< img src="ja-specific/alert_3.png" >}}

アラートを設定するための6ステップ

1. アラートのためのメトリクスを定義します。`average`, `minimum`, `maximum`, `sum`などの集計機能を指定し、次に対象となるメトリクスを指定します。メトリクス集計の対象となる`hosts:`,
`tags:`, `regions:`, `devices:`などの範囲パラメーターを指定していきます。
2. `simple`か`multi alert`を選択します; [`multi-alerts`](#simple) は、非常に柔軟なアラートの設定ができます。
例えば、[ by `host,device` ]と設定することで、各ホストの異なるdeviceの情報により、別々にアラートを設定することもできます。
3. アラートの発生条件を設定します; しきい値を`above`(上回った状態)か`below`(下回った状態)かを指定し、次に該当条件の発生頻度を`on average`(平均で),`at least once`(一回でも), or `at all times`(恒常的に)で指定し、最後に継続時間を指定します。
4. アラートに使っているメトリクスが受信できなくなった際に通知するなどのオプションを設定します。
5. アラートの発生時のタイトルとメッセージ本文を設定します。
6. Pagerduty, slack, HipChat などの外部サービスやチームメンバを、アラート送信先として設定します。


<!-- <h4 id="metric">Choosing a metric</h4>
{{< img src="alert_4.png" >}}
<p>To get into detail on how to use alerts, let's take aws.elb.latency as an example.
Let's also say there are 5 hosts, 1 in staging and the rest in prod.</p>

<p>When selecting 'avg' for 'aws.elb.latency', Datadog will be taking the average
from all hosts emitting that metric. If you add a parameter to 'over', say 'env:prod',
it will now stop averaging in anything not tagged 'env:prod'. On the other hand, if you
did 'env:staging' it wouldn't really be averaging because there would be just one host
emitting that metric thus one time series.
        When selecting 'max' or 'min', Datadog will take the highest or
lowest point respectively seen from any of the hosts emitting that metric.
        Select a 'tag' will limit the metric collection to what you've specified.</p> -->

<h4 id="metric">１．１ メトリクスの指定</h4>

{{< img src="alert_4.png" >}}

アラートの使い方の詳しい解説のために、`aws.elb.latency` メトリクスを監視するアラートを設定します。
監視対象ホストは、合計5台。1台がステージング用(`env:staging`)で、残りの4台を運用環境(`env:prod`)に使用しているとします。

[ `avg` of `aws.elb.latency` ] を指定した場合は、Datadogでは全てのホストが送信している`aws.elb.latency`の平均値を算出します。次に、[ `avg` of `aws.elb.latency` over `env:prod` ]を指定した場合は、`env:prod`(運用環境)とタグ付けされたホストのみが平均値の算出に使われます。その逆に、[ `avg` of `aws.elb.latency` over `env:staging` ]と指定すると、ステージング用の1台のホストの`aws.elb.latency`になります。

`max`又は`min`を指定した場合は、範囲指定に入っているホストの`aws.elb.latency` メトリクスの最高値か最低値を取得します。

又、overの次に`tag:`や`host:`を指定することで、`env:`と同様に対象となるホストを絞り込むことができます。


<!-- <h4 id="change">Change Alerts</h4>
{{< img src="change_alert.png" >}}
<p>
A change alert is triggered when the delta between values is higher than the threshold.
</p>
<p>
A change alert evaluates the difference between a value N minutes ago and now.
The averaging of the values depends on the timeframe of the alert, e.g. 5m - 30m
alerts calculate against a 1-minute moving average, while 1h - 4h alerts will use a 10-minute moving average.
</p>
<p>
On each alert evaluation Datadog will calculate the raw difference (not absolute value) between these two values.
An alert is triggered when the delta over time exceeds the limit, where the limit can be a fixed value or a percent of change.

</p> -->

<h4 id="change">１．２ メトリクスの変化量によるアラート</h4>
{{< img src="change_alert.png" >}}

メトリクスの差分がしきい値より高い場合、アラートを発生します。

`Change Alert`では、[ from `5 minutes` ago ] と指定した場合、[ compared to a `N` minute average ]で表示されている`N`分前と現在の差分を評価します。from以降の指定値が、5分~30分の場合は1分の移動平均(1-minute moving average)になります。1時間~4時間の場合は10分の移動平均(10-minute moving average)、24時間の場合は1時間の移動平均(1-hour moving average)となります。

各アラート評価タイミングで、2点間の値の差分を符号なしで算出します。その後、指定時間内の変化( `change`(値) 又は`%change`(率) )に基づき、しきい値と比較しアラートを発生します。


<!-- <h4 id="simple">Simple and Multi Alerts</h4>
{{< img src="alert_5.png" >}}
<p>A simple alert aggregates over all reporting sources.
You'll get one alert, when the aggregated value meets the conditions set below.
This works best to monitor a metric from a single host, like avg of system.cpu.iowait
over host:bits, or for an aggregate metric across many hosts, like
sum of nginx.bytes.net over region:us-east.</p>

<p>A multi alert applies the alert to each source, according to your group parameters.
E.g. to alert on disk space you might group by host and device, creating the
query: avg of system.disk.in_use over * by host,device. This will trigger a separate
alert for each device on each host that is running out of space.</p> -->

<h4 id="simple">２．Simple Alert 又は Multi Alert</h4>

{{< img src="alert_5.png" >}}

`simple alert`は、指定した条件のどれかを満たせば、アラートを発生します。
アラートは1種類のみで、収集したメトリクスが条件に合致した時に発生されます。

例えば、1台のホストからのみ送信されるメトリクス, [ `sum` of `nginx.bytes.net` over `region:us-east` ]という全ホストの平均値, [ `avg` of `system.cpu.iowait`over `host:bits` ]という平均値を監視している場合などに適しています。

`multi alert`は、**group parameters**(メトリクス収集範囲)に関連して指定した条件に基づいて、個別のアラートを発生します。

例えば、ディスク容量のアラートでは、[ `avg `of `system.disk.in_use` over `*`] と [ `Multi Alert` Trigger a separate alert for each `host:*`,`device:*` ]を組み合わせて、各ホストの各ディバイスごとにアラートを発生する場合に適しています。


<!-- <h4 id="conditions">Alert conditions</h4>
{{< img src="alert_5_1.png" >}}

<p>When setting the alert conditions, ensure that the threshold value you use correctly
matches the unit that are showing in the graph (for example, bytes vs GB). The
'on average' option updates each minute to include the most recent time and dropoff
what’s old (not a “moving average”, really a sliding timeframe). The options for
the time frame are 5, 10, 15, 30, or 60 minutes.</p> -->

<h4 id="conditions">３．アラート条件の設定</h4>
{{< img src="alert_5_1.png" >}}

アラート条件を設定する場合は、グラフに表示されている単位と、しきい値の単位が一致していることを確認してください。( 例えば:、bytes vs GB ）`on average` オプションでは、1分ごとに新しい値を追加し、最も古い値を削除して平均値を更新していきます。(時間軸を実際にずらす方式です、移動平均ではありません)このオプションが有効なのは、`5 minutes`, `10 minutes`, `15 minutes`, `30 minutes`, `60 minutes`の場合になります。

<!-- <h4 id="nodata">No-data alerts</h4>

<p>No-data alerts make it possible to be alerted on a host going down by
registering a “simple alert” or “multi alert” for a metric that is expected to be
reported all the time. Thanks to tags and multi-alerts, this still allows
one alert to cover a large number of hosts. You can enable this by selecting
"Notify" in the dropdown beside "if this metric is missing data for more than
your selected timeframe."</p>

<p>On the other hand, if you are monitoring a
metric over an auto-scaling group of hosts that may come and go at any time,
you will get a lot of notifications when hosts are shut down deliberately. In
that case you should not enable notifications for missing data.</p>

<p>This does not entirely encompass host up/down scenarios, because in the
event that the agent died and the host is still up, you would be incorrectly
alerted. We are working towards supporting this with Service Checks, a feature
we hope to release in the near future.</p> -->

<h4 id="nodata">４．１ メトリクスが届かなくなった時の通知</h4>

普通の状態のホストでは、メトリクスデータを恒常的に送信していることを想定している場合は、そのデータが届かなく成った場合の通知は非常に便利です。例えば、ホストにインストールされたDatadog Agent は、常に動作し、`system.cpu.iowait` というメトリクス名でiowait値を恒常的に送信してきているはずです。このiowaitの値がしきい値を超えた時に発生するアラートを設定していた場合、データーがDatadog側に届かなくなったことの通知を出しておくことは、アラート機能の停止の可能性を知る上で、重要な情報となります。

しかしならがら、ホストから自動で増減するオートスケールグループから送られてくるメトリクスデータの監視では、意図的に不要になった複数のホストを停止した時には、大量の通知を受けることになるでしょう。そのような場合は、通知を無効にすることを検討しても良いかもしれません。


<!-- <h4 id="autoresolve">Autoresolving alerts</h4>
{{< img src="alert_auto.png" >}}

<p>For some metrics that report periodically across different tags,
it may make sense to have triggered alerts auto-resolve after a certain time
period. For example, if you have a counter that reports only when an error is
fired the alert will never resolve because the metric will never report 0 as
the number of errors. In this case, you may want to set your alert to resolve
after 4 hours of inactivity on that metric.
In most cases this setting will not be useful because you will only want an
alert to resolve once it actually has been fixed. So in the general case it
makes sense to leave this as [Never] so that the alert will only resolve when
the metric falls below the given threshold.</p> -->

<h4 id="autoresolve">４．２ アラートの自動解除</h4>
{{< img src="alert_auto.png" >}}

異なるタグに股がって、定期的に送信されるメトリクスを基にしたアラートでは、発生したアラートを一定時間後に解除することが合理的かもしれません。例えば、エラーの発生回数を送信するカウンタでは、エラー発生数は0を送信することはないので、アラートは解除されることはありません。このようなケースでは、4時間経過後もメトリクスの変化がなければ、アラートが解除されるように設定しておくと良いかもしれません。

特定の場合をのぞいて自動解除の設定は、あまりオススメではありません。なぜならばアラートは、「実際に問題が修正された時のみ」解除したいからです。
従って一般的なケースでは、この部分の設定を`Never` にしておき、メトリクスがしきい値以下に戻った際に、アラートが解除されるようにします。


<!-- <h4 id="notify">Notifying</h4>
{{< img src="alert_6.png" >}}
<p>In the final step of setting up an alert, you can give any necessary commentary
for the alert so if it triggers it will alert the correct people with the most context possible.
Each graph delivered via email or in the event stream when an alert triggers is a hyperlink
to that graph at that exact timeframe. In the body of the message you can <a href="https://docs.datadoghq.com/faq/#notify">@notify</a>
members of the team to have it specifically call out to them. You can also do this in
part 5, 'Notify your team'.</p> -->

<h4 id="notify">５．アラートメッセージの設定</h4>

{{< img src="alert_6.png" >}}

アラート設定の最後は、発生するアラートに添付するコメントの設定です。
このコメントには、発生したアラートに最も対応してほしい人の注目を喚起するために適切な情報が含まれている必要があります。
メール添付によって配信されたグラフやイベントストリームに表示されるグラフは、アラートが発生した瞬間をキャプチャーしたグラフへのハイパーリンクにもなっています。

<h4 id="notify1">６．アラート送信先の設定</h4>


メッセージ本文では、Datadogの<a href="https://docs.datadoghq.com/ja/faq/#notify">@notify</a>機能を埋め込んで、特定のメンバにのみ連絡が届くようにすることもできます。この特定のメンバへのアラートは、設定項目のパート5も同じことができるようになっています。


<!-- <h3 id="faqs">Alerting FAQs</h3>
<ul>
<li>Can you alert on a function? Not currently, but we're working towards this!</li>
<li>Can you alert on an event? Not currently, but we're discussing how we'd like to implement this.
As an alternative you can set up an @ notification in the body of the event which would deliver the
event via email whenever it occurred.</li>

</ul> -->
