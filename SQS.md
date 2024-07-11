## Setting up a mirrord operator with an SQS Splitter


Follow the AWS guide to create an IAM role for the operator's service account:
https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts.html

```
mv ~/Downloads/universal-apple-darwin.zip ~/Downloads/universal-apple-darwin-old.zip
```

Download a new mirrord binary from 
https://github.com/metalbear-co/mirrord/actions/runs/9891152141/artifacts/1690856327

```
unzip ~/Downloads/universal-apple-darwin
xattr -d com.apple.quarantine ~/Downloads/universal-apple-darwin/mirrord
chmod u+x ~/Downloads/universal-apple-darwin/mirrord
cp ~/Downloads/universal-apple-darwin/mirrord ~/Library/Application\ Support/JetBrains/IntelliJIdea2024.1/plugins/mirrord/bin/macos/mirrord
```


Clone the chart's repo's fork, or add it as a new remote.
```
git clone git@github.com:t4lz/metalbear-charts.git
```
or alternatively
```
git remote add t4lz git@github.com:t4lz/metalbear-charts.git
git fetch t4lz sqs
```

and then checkout the sqs branch
```
git checkout sqs
```


reinstall helm chart

create a queue registry:

```yaml
apiVersion: "queues.mirrord.metalbear.co/v1alpha"
kind: MirrordWorkloadQueueRegistry
metadata:
  name: first-ever-splitter
spec:
  queues:
    manual-queue:
      queueType: SQS
      envVar: SQS_TEST_Q_NAME
  consumer: 
    rollout: sqs-printer
  tags:
    who: me
    when: now
```


To see operator logs:

```
kubectl logs -n mirrord -l app=mirrord-operator --tail -1 | less -R
```
