# Build and Secure Networks in Google Cloud: Challenge Lab

### Task 1. Remove the overly permissive rules.

In this task you only need to the open-access firewall rules.

- In the Cloud Console, navigate to **Menu** > **VPC Network** > **Firewall**.
- Check the box next to the rule named `open-access`.
- Click on **DELETE** to remove it.

## OR

### USING COMMAND LINE:

Enter the following command line in the Google Cloud Shell.

- `gcloud compute firewall-rules delete open-access`
  If prompted _Do you want to continue (Y/n)_, type Y and Press **Enter**.

### Task 2. Start the bastion host instance.

- In the Cloud Console, navigate to **Menu** > **Compute Engine** > **VM instances**.
- Check the box next to the instance named `bastion`.
- Click on _Start_ to run the instance.

## OR

### USING COMMAND LINE:

Enter the following command line in the Google Cloud Shell.

- `gcloud compute instances start bastion`

### Task 3. Create a firewall rule that allows SSH (tcp/22) from the IAP service and add network tag on bastion.

#### Adding network tag on bastion:

- On the **VM instances** page, click on the name of the **bastion** instance.
- Click **EDIT** on the details page.
- Add **bastion** to the Network tags field.
- Scroll to the button of the page and click **Save**.

#### Creating firewall rule to allow SSH form the IAP service:

- Go to the Firewall Rules page, and click **Create firewall rule**.

Configure the following settings:

```
Field and Value
Name:                   allow-ssh-from-iap
Direction of traffic:   Ingress
Network-type            acme-vpc
Target:                 Specified target tags
Target tags:            bastion
Source IP ranges:       35.235.240.0/20
Protocols and ports:    Select TCP, enter 22 to allow SSH
```

# ![img5a](./Assets/img5a.png)

## OR

### USING COMMAND LINE:

Enter the following command line in the Google Cloud Shell.

- `gcloud compute firewall-rules create ssh-ingress --allow=tcp:22 --source-ranges 35.235.240.0/20 --target-tags ssh-ingress --network acme-vpc`

- `gcloud compute instances add-tags bastion --tags=ssh-ingress --zone=us-central1-b`

### Task 4. Create a firewall rule that allows traffic on HTTP (tcp/80) to any address and add network tag on `juice-shop`.

Create firewall rule to allow HTTP traffic to `juice-shop`.

- On the Firewall Rules page, and click **Create firewall rule**.

Configure the following settings:

```
Field and Value
Name:                   allow-http-ingress
Direction of traffic:   Ingress
Network-type            acme-vpc
Targets:                Specified target tags
Target tags:            juice-shop
Source IP ranges:       0.0.0.0/0
Protocols and ports:    Select TCP, enter 80 to allow HTTP
```

# ![img5b](./Assets/img5b.png)

#### Adding network tag on `juice-shop`.

- On the **VM instances page**, click on the name of the `juice-shop` instance.
- Click **EDIT** on the details page.
- Add `juice-shop` to the Network tags field.
- Scroll to the button of the page and click **Save**.

## OR

### USING COMMAND LINE:

Enter the following command line in the Google Cloud Shell.

- `gcloud compute firewall-rules create http-ingress --allow=tcp:80 --source-ranges 0.0.0.0/0 --target-tags http-ingress --network acme-vpc`

- `gcloud compute instances add-tags juice-shop --tags=http-ingress --zone=us-central1-b`

### Task 5. Create a firewall rule that allows traffic on SSH (tcp/22) from `acme-mgmt-subnet` network address and add network tag on `juice-shop`.

- Navigate to **VPC network** > **VPC networks**.
- Copy the IP address range of the `acme-mgmt-subnet`.
- Go back to the Firewall Rules page, and click **Create firewall rule**.

Configure the following settings:

```
Field and Value
Name:                   allow-ssh-from-mgmt-subnet
Direction of traffic:   Ingress
Network-type            acme-vpc
Targets:                Specified target tags
Target tags:            `bastion` and `juice-shop`
Source IP ranges:       IP address range of your `aceme-mgmt-subnet`
Protocols and ports:    Select TCP and enter 22 to allow SSH
```

# ![img5c](./Assets/img5c.png)

## OR

### USING COMMAND LINE:

Enter the following command line in the Google Cloud Shell.

- `gcloud compute firewall-rules create internal-ssh-ingress --allow=tcp:22 --source-ranges 192.168.10.0/24 --target-tags internal-ssh-ingress --network acme-vpc`

- `gcloud compute instances add-tags juice-shop --tags=internal-ssh-ingress --zone=us-central1-b`

### Task 6: In the Compute Engine instances page, click the SSH button for the bastion host. Once connected, SSH to `juice-shop`.

After configuring the firewall rules, try to verify the environment via the bastion.

- Navigate to **Compute Engine** > **VM instances**.
- Copy the Internal IP of the `juice-shop` instance.
- Click on the **SSH** button in the row of the bastion instance.
- In the **SSH** console, access the `juice-shop` from the bastion using the following command:
  `ssh < internal-IP-of-juice-shop >`
- press yes

**_(Remember to REPLACE < internal-IP-of-juice-shop > with the copied IP address)_**
