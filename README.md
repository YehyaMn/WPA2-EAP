# MikroTik WPA2-Enterprise (EAP) with Windows Server 2012 RADIUS

This guide provides a step-by-step process to configure **MikroTik as a WPA2-EAP Access Point** using **Windows Server 2012 RADIUS (NPS)**.

---

## **1. Setup Windows Server 2012 RADIUS (NPS)**

### **Step 1: Install Network Policy Server (NPS)**
1. Open **Server Manager** → Click **Manage** → **Add Roles and Features**.
2. Select **Role-based or feature-based installation**.
3. Select the **Network Policy and Access Services** role.
4. Check **Network Policy Server** and complete the installation.

### **Step 2: Configure NPS as a RADIUS Server**
1. Open **Network Policy Server (NPS)** from **Administrative Tools**.
2. Expand **RADIUS Clients and Servers** → Right-click **RADIUS Clients** → Click **New**.
3. Enter a **Friendly Name** (e.g., "MikroTik AP").
4. Set **IP Address** to the MikroTik router’s IP.
5. Choose **RADIUS Shared Secret** and enter a strong key (this will be used in MikroTik).
6. Click **OK**.

### **Step 3: Configure a Network Policy for WPA2-EAP**
1. In **NPS**, go to **Policies** → **Network Policies** → Right-click **New**.
2. Name the policy **MikroTik WPA2-Enterprise**.
3. Set **Type of network access server** to **Unspecified**.
4. Click **Add Conditions** → Choose **Windows Groups** → Select a security group (e.g., "WiFi Users").
5. Under **Authentication Methods**, check **Microsoft: Protected EAP (PEAP)**.
6. Click **Configure** next to PEAP, and select the **SSL Certificate** issued by the AD Certificate Services.
7. Click **Next** → **Grant Access** → **Next**.
8. Set **Encryption Settings**: Enable **AES and TKIP**.
9. Click **Finish**.

---

## **2. Configure MikroTik for WPA2-EAP with RADIUS**

### **Step 1: Create the Wireless Security Profile**
1. Open **MikroTik WinBox**.
2. Navigate to **Wireless** → **Security Profiles** → Click **+**.
3. Name it **WPA2-EAP**.
4. Set **Mode** to **Dynamic Keys**.
5. Check **WPA2-EAP** (uncheck WPA-PSK).
6. Under **EAP Methods**, select **PEAP, TLS, TTLS**.
7. Under **Certificates**, select the **CA certificate** exported from the Windows server.
8. Click **OK**.

### **Step 2: Configure the Wireless Interface**
1. Go to **Wireless** → **Interfaces**.
2. Select the **WiFi interface** → Click **Wireless** tab.
3. Set **Mode** to **AP Bridge**.
4. Set **SSID** (e.g., "Enterprise-WiFi").
5. Select **WPA2-EAP** under the **Security Profile**.
6. Click **Apply** → **OK**.

### **Step 3: Configure RADIUS on MikroTik**
1. Navigate to **RADIUS** → Click **+**.
2. Enable **Service** for **wireless**.
3. Set **Address** to the IP of the Windows Server running NPS.
4. Enter the **Secret** (same as the one used in NPS).
5. Set **Authentication Port** to **1812** and **Accounting Port** to **1813**.
6. Click **Apply** → **OK**.

### **Step 4: Enable RADIUS Authentication in Wireless**
1. Go to **Wireless** → **Interfaces** → Select your WiFi interface.
2. Enable **RADIUS MAC Authentication** (if using MAC authentication).
3. Click **Apply** → **OK**.

---

## **3. Configure Windows Clients**
1. On the Windows client, go to **Wi-Fi settings**.
2. Select the **Enterprise-WiFi** SSID.
3. Choose **WPA2-Enterprise with PEAP**.
4. If prompted, select **Do not validate server certificate** (or manually trust the CA).
5. Enter **domain credentials** (Username & Password).
6. Connect and verify access.

---

## **4. Troubleshooting Tips**
### **MikroTik Logs**:
- Run `log print` in MikroTik terminal to check authentication errors.

### **Check NPS Logs**:
- Open **Event Viewer** → Go to **Custom Views** → **Server Roles** → **Network Policy and Access Services**.
- Look for authentication failures.

### **Check Connectivity**:
- Use `ping` from MikroTik to Windows Server to confirm network reachability.
- Ensure UDP ports **1812** and **1813** are open on Windows Firewall.

---

## **Conclusion**
This guide outlines the full setup for WPA2-Enterprise using **MikroTik as an AP** and **Windows Server 2012 as the RADIUS server**. If configured correctly, clients will authenticate using their **Active Directory credentials** for secure network access.

