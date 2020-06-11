# Install UniquID CLI

### Update Python

Make sure you're running Python 3+ and pip3. For setup instructions, see:

{% page-ref page="prerequisites.md" %}

### Install package from pip3

```bash
sudo pip3 install uniquid
```

{% hint style="info" %}
For more details on the `uniquid` package, click [here](https://pypi.org/project/uniquid/)
{% endhint %}

### Verify version

Make sure `uniquid` CLI client has been installed and is on the latest stable version

```bash
uniquid --version
```

### Configure CLI

Navigate to directory where you downloaded the `credentials.json` file

```bash
cd /path/to/file/
```

{% hint style="info" %}
The step below is only required if you installed UniquID CLI on an AWS EC2 instance. Otherwise, click [here](install-cli.md#check-login) to skip this step.
{% endhint %}

You must upload your credentials to your EC2 instance. There are 2 options for this:

**Option 1 -**  Use scp to copy the UniquID credentials file from your development machine to the EC2 instance:

```bash
scp -i <user-key-pair.pem> credentials.json ubuntu@<ec2-instance-ip-address>:.
```

**Option 2** - Cut and paste the contents of the UniquID credentials file after you SSH into the instance:

```bash
cat > ~/credentials.json
--paste the content of the file--
press <enter> press CTRL D
```

### Check login

Login to UniquID with the `credentials.json` file and check the status

```bash
uniquid login --credentials-file ~/credentials.json
uniquid status --verbose
```



