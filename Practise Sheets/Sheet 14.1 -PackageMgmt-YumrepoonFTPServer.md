Setting up a local YUM repository on an FTP server involves several steps.

### Prerequisites
- A Linux server with FTP server software (like vsftpd) installed and running.
- A directory on the FTP server where the YUM repository will be stored.
- The `createrepo` package installed on your server.

### Step 1: Install Required Packages
First, ensure that the necessary packages are installed. You might need to install `createrepo_c` if it’s not already installed.

```bash
sudo yum install createrepo_c vsftpd
```

### Step 2: Create a Directory for the YUM Repository
Create a directory where the RPM packages and repository metadata will be stored.

```bash
sudo mkdir -p /var/ftp/pub/yumrepo
```

### Step 3: Copy RPM Packages
Copy your RPM packages to the newly created directory.

```bash
sudo cp /path/to/your/rpms/*.rpm /var/ftp/pub/yumrepo/
```

### Step 4: Create Repository Metadata
Navigate to the YUM repository directory and create the repository metadata using `createrepo_c`.

```bash
cd /var/ftp/pub/yumrepo
sudo createrepo_c .
```

### Step 5: Configure FTP Server
Edit the FTP server configuration file (for vsftpd, it’s usually `/etc/vsftpd/vsftpd.conf`) to ensure the directory is accessible.

```bash
# Ensure these lines are set
anonymous_enable=YES
local_enable=NO
write_enable=NO
```

After editing, restart the FTP service to apply the changes:

```bash
sudo systemctl restart vsftpd
```

### Step 6: Configure Firewall
If you have a firewall running, make sure to allow FTP traffic.

```bash
sudo firewall-cmd --add-service=ftp --permanent
sudo firewall-cmd --reload
```

### Step 7: Set Up the YUM Repository on Client Machines
On the client machines that will use this YUM repository, create a `.repo` file in `/etc/yum.repos.d/`. For example, create a file named `localrepo.repo`.

```bash
sudo vi /etc/yum.repos.d/localrepo.repo
```

Add the following content, adjusting the URL as needed:

```ini
[localrepo]
name=Local YUM Repository
baseurl=ftp://<ftp-server-ip>/pub/yumrepo/
enabled=1
gpgcheck=0
```

### Step 8: Test the Configuration
To test the setup, run:

```bash
sudo yum clean all
sudo yum repolist
```

You should see your local repository listed. You can now install packages from it using YUM.

### Conclusion
You have successfully set up a local YUM repository on an FTP server. This setup allows client machines to retrieve RPM packages from your local repository efficiently.
