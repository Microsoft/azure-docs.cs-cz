---
title: 'Připojení počítače k virtuální síti typu Point-to-Site s použitím ověření certifikátu: Portál Azure Classic | Dokumentace Microsoftu'
description: Vytvoříte připojení classic k bráně VPN typu Point-to-Site pomocí portálu Azure Portal.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: d28893133c27fe4945918071c60b889e997b775b
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424153"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Configure a Point-to-Site connection by using certificate authentication (classic)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

This article shows you how to create a VNet with a Point-to-Site connection. You create this Vnet with the classic deployment model by using the Azure portal. Tato konfigurace používá certifikáty k ověření připojujícího se klienta, buď podepsané svým držitelem (self-signed certificate), nebo vydané certifikační autoritou. You can also create this configuration with a different deployment tool or model by using options that are described in the following articles:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

You use a Point-to-Site (P2S) VPN gateway to create a secure connection to your virtual network from an individual client computer. Point-to-Site VPN connections are useful when you want to connect to your VNet from a remote location. When you have only a few clients that need to connect to a VNet, a P2S VPN is a useful solution to use instead of a Site-to-Site VPN. Připojení VPN P2S se vytváří jeho zahájením z klientského počítače.

> [!IMPORTANT]
> Model nasazení Classic podporuje pouze klienty VPN systému Windows a používá protokol SSTP (Secure Socket Tunneling Protocol), což je protokol VPN založený na protokolu SSL. To support non-Windows VPN clients, you must create your VNet with the Resource Manager deployment model. Model nasazení Resource Manager podporuje kromě protokolu SSTP i IKEv2 VPN. Další informace najdete v tématu [Informace o připojeních typu Point-to-Site](point-to-site-about.md).
>
>

![Diagram Point-to-Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="prerequisites"></a>Předpoklady

Point-to-Site certificate authentication connections require the following prerequisites:

* Dynamickou bránu VPN.
* Veřejný klíč (soubor .cer) pro kořenový certifikát nahraný do Azure. This key is considered a trusted certificate and is used for authentication.
* Klientský certifikát vygenerovaný z kořenového certifikátu a nainstalovaný na každém klientském počítači, který se bude připojovat. Tento certifikát se používá k ověřování klienta.
* Na každém klientském počítači, který se bude připojovat, musí být vygenerován a nainstalován balíček pro konfiguraci klienta VPN. The client configuration package configures the native VPN client that's already on the operating system with the necessary information to connect to the VNet.

Point-to-Site connections don't require a VPN device or an on-premises public-facing IP address. Připojení VPN se vytváří přes protokol SSTP (Secure Socket Tunneling Protocol). Na straně serveru podporujeme SSTP verze 1.0, 1.1 a 1.2. Klient rozhodne, která verze se má použít. Pro Windows 8.1 a novější se standardně používá SSTP verze 1.2. 

For more information about Point-to-Site connections, see [Point-to-Site FAQ](#point-to-site-faq).

### <a name="example-settings"></a>Příklad nastavení

Use the following values to create a test environment, or refer to these values to better understand the examples in this article:

- **Create virtual network (classic) settings**
   - **Name**: Enter *VNet1*.
   - **Address space**: Enter *192.168.0.0/16*. V tomto příkladu používáme pouze jeden adresní prostor. Pro svoji virtuální síť můžete mít více adresních prostorů, jak ukazuje diagram.
   - **Subnet name**: Enter *FrontEnd*.
   - **Subnet address range**: Enter *192.168.1.0/24*.
   - **Subscription**: Select a subscription from the list of available subscriptions.
   - **Resource group**: Enter *TestRG*. Select **Create new**, if the resource group doesn't exist.
   - **Location**: Select **East US** from the list.

  - **VPN connection settings**
    - **Connection type**: Select **Point-to-site**.
    - **Client Address Space**: Enter *172.16.201.0/24*. VPN clients that connect to the VNet by using this Point-to-Site connection receive an IP address from the specified pool.

- **Gateway configuration subnet settings**
   - **Name**: Autofilled with *GatewaySubnet*.
   - **Address range**: Enter *192.168.200.0/24*. 

- **Gateway configuration settings**:
   - **Size**: Select the gateway SKU that you want to use.
   - **Routing Type**: Select **Dynamic**.

## <a name="create-a-virtual-network-and-a-vpn-gateway"></a>Vytvoření virtuální sítě a brány VPN

Before you begin, verify that you have an Azure subscription. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>Část 1: Vytvoření virtuální sítě

If you don't already have a virtual network (VNet), create one. Snímky obrazovek slouží jen jako příklady. Nezapomeňte hodnoty nahradit vlastními. Pokud chcete vytvořit virtuální síť přes Azure Portal, použijte následující postup:

1. On the [Azure portal](https://portal.azure.com) menu or from the **Home** page, select **Create a resource**. The **New** page opens.

2. In the **Search the marketplace** field, enter *virtual network* and select **Virtual network** from the returned list. The **Virtual network** page opens.

3. From the **Select a deployment model** list, select **Classic**, and then select **Create**. The **Create virtual network** page opens.

4. Na stránce **Vytvořit virtuální síť** nakonfigurujte nastavení virtuální sítě. Na této stránce přidáte první adresní prostor a jeden rozsah adres podsítě. Po dokončení vytváření sítě VNet můžete přejít zpět a přidat další podsítě a adresní prostory.

   ![Stránka pro vytvoření virtuální sítě](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)

5. Select the **Subscription** you want to use from the drop-down list.

6. Select an existing **Resource Group**. Or, create a new resource group by selecting **Create new** and entering a name. If you're creating a new resource group, name the resource group according to your planned configuration values. Další informace o skupinách prostředků najdete v tématu [Přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md#resource-groups).

7. Select a **Location** for your VNet. This setting determines the geographical location of the resources that you deploy to this VNet.

8. Select **Create** to create the VNet. From the **Notifications** page, you'll see a **Deployment in progress** message.

8. After your virtual network has been created, the message on the **Notifications** page changes to **Deployment succeeded**. Select **Pin to dashboard** if you want to easily find your VNet on the dashboard. 

10. Přidejte server DNS (volitelné). Po vytvoření virtuální sítě můžete přidat IP adresu serveru DNS pro překlad IP adres. Zadaná IP adresa serveru DNS by měla být adresa serveru DNS, který dokáže přeložit názvy pro prostředky ve vaší virtuální síti.

    To add a DNS server, select **DNS servers** from your VNet page. Then, enter the IP address of the DNS server that you want to use and select **Save**.

### <a name="part-2-create-a-gateway-subnet-and-a-dynamic-routing-gateway"></a>Part 2: Create a gateway subnet and a dynamic routing gateway

In this step, you create a gateway subnet and a dynamic routing gateway. In the Azure portal for the classic deployment model, you create the gateway subnet and the gateway through the same configuration pages. Use the gateway subnet for the gateway services only. Nikdy nenasazujte nic (například virtuální počítače nebo jiné služby) přímo do podsítě brány.

1. In the Azure portal, go to the virtual network for which you want to create a gateway.

2. On the page for your virtual network, select **Overview**, and in the **VPN connections** section, select **Gateway**.

   ![Select to create a gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Na stránce **Nové připojení VPN** vyberte **Point-to-Site**.

   ![Připojení typu Point-to-Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. For **Client Address Space**, add the IP address range from which the VPN clients receive an IP address when connecting. Use a private IP address range that doesn't overlap with the on-premises location that you connect from, or with the VNet that you connect to. You can overwrite the autofilled range with the private IP address range that you want to use. This example shows the autofilled range. 

   ![Klientský adresní prostor](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Select **Create gateway immediately**, and then select **Optional gateway configuration** to open the **Gateway configuration** page.

   ![Select optional gateway configuration](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)

6. From the **Gateway configuration** page, select **Subnet** to add the gateway subnet. It's possible to create a gateway subnet as small as /29. However, we recommend that you create a larger subnet that includes more addresses by selecting at least /28 or /27. Doing so will allow for enough addresses to accommodate possible additional configurations that you may want in the future. Při práci s podsítěmi brány nepřidružujte skupinu zabezpečení sítě (NSG) k podsíti brány. Associating a network security group to this subnet may cause your VPN gateway to not function as expected. Select **OK** to save this setting.

   ![Přidání podsítě brány](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Vyberte **velikost** brány. Velikost je skladová položka brány pro vaši bránu virtuální sítě. In the Azure portal, the default SKU is **Default**. For more information about gateway SKUs, see [About VPN gateway settings](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

   ![Velikost brány](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Vyberte pro bránu **typ směrování**. Konfigurace P2S vyžadují **dynamický** typ směrování. Select **OK** when you've finished configuring this page.

   ![Konfigurace typu směrování](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)

9. On the **New VPN Connection** page, select **OK** at the bottom of the page to begin creating your virtual network gateway. A VPN gateway can take up to 45 minutes to complete, depending on the gateway SKU that you select.
 
## <a name="generatecerts"></a>Create certificates

Azure uses certificates to authenticate VPN clients for Point-to-Site VPNs. Nahrajete do Azure informace o veřejném klíči kořenového certifikátu. The public key is then considered *trusted*. Client certificates must be generated from the trusted root certificate, and then installed on each client computer in the Certificates-Current User\Personal\Certificates certificate store. The certificate is used to authenticate the client when it connects to the VNet. 

If you use self-signed certificates, they must be created by using specific parameters. You can create a self-signed certificate by using the instructions for [PowerShell and Windows 10](vpn-gateway-certificates-point-to-site.md), or [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). It's important to follow the steps in these instructions when you use self-signed root certificates and generate client certificates from the self-signed root certificate. Otherwise, the certificates you create won't be compatible with P2S connections and you'll receive a connection error.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Acquire the public key (.cer) for the root certificate

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Vygenerování klientského certifikátu

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Nahrání souboru .cer kořenového certifikátu

After the gateway has been created, upload the .cer file (which contains the public key information) for a trusted root certificate to the Azure server. Don't upload the private key for the root certificate. After you upload the certificate, Azure uses it to authenticate clients that have installed a client certificate generated from the trusted root certificate. You can later upload additional trusted root certificate files (up to 20), if needed.  

1. On the **VPN connections** section of the page for your VNet, select the clients graphic to open the **Point-to-site VPN connection** page.

   ![Klienti](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. On the **Point-to-site VPN connection** page, select **Manage certificate** to open the **Certificates** page.

   ![Stránka certifikátů](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

1. On the **Certificates** page, select **Upload** to open the **Upload certificate** page.

    ![Stránka pro nahrání certifikátů](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)

4. Select the folder graphic to browse for the .cer file. Select the file, then select **OK**. The uploaded certificate appears on the **Certificates** page.

   ![Nahrání certifikátu](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)


## <a name="configure-the-client"></a>Konfigurace klienta

To connect to a VNet by using a Point-to-Site VPN, each client must install a package to configure the native Windows VPN client. Konfigurační balíček konfiguruje nativního klienta VPN ve Windows pomocí nastavení potřebných pro připojení k virtuální síti.

V každém klientském počítači můžete použít stejný konfigurační balíček klienta VPN za předpokladu, že jeho verze odpovídá architektuře klienta. For the list of client operating systems that are supported, see the [Point-to-Site connections FAQ](#point-to-site-faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Generate and install a VPN client configuration package

1. In the Azure portal, in the **Overview** page for your VNet, in **VPN connections**, select the client graphic to open the **Point-to-site VPN connection** page.

2. From the **Point-to-site VPN connection** page, select the download package that corresponds to the client operating system where it's installed:

   * U 64bitových klientů vyberte **Klient VPN (64bitový)** .
   * U 32bitových klientů vyberte **Klient VPN (32bitový)** .

   ![Stažení balíčku pro konfiguraci klienta VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)

3. After the package generates, download it and then install it on your client computer. If you see a SmartScreen popup, select **More info**, then select **Run anyway**. Můžete také balíček uložit k instalaci na další klientské počítače.

### <a name="install-a-client-certificate"></a>Install a client certificate

To create a P2S connection from a different client computer than the one used to generate the client certificates, install a client certificate. When you install a client certificate, you need the password that was created when the client certificate was exported. Typically, you can install the certificate by just double-clicking it. Další informace najdete v tématu [Instalace exportovaného klientského certifikátu](vpn-gateway-certificates-point-to-site.md#install).


## <a name="connect-to-your-vnet"></a>Připojení k síti VNet

>[!NOTE]
>Na klientském počítači, že kterého se připojujete, musíte mít oprávnění správce.
>
>

1. To connect to your VNet, on the client computer, go to **VPN connections** in the Azure portal and locate the VPN connection that you created. The VPN connection has the same name as your virtual network. Vyberte **Connect** (Připojit). If a pop-up message about the certificate appears, select **Continue** to use elevated privileges.

2. On the **Connection** status page, select **Connect** to start the connection. If you see the **Select Certificate** screen, verify that the displayed client certificate is the correct one. If not, select the correct certificate from the drop-down list, and then select **OK**.

3. If your connection succeeds, you'll see a **Connected** notification.


### <a name="troubleshooting-p2s-connections"></a>Řešení potíží s připojeními P2S

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Ověření připojení VPN

1. Verify that your VPN connection is active. Open an elevated command prompt on your client computer, and run **ipconfig/all**.
2. Zkontrolujte výsledky. Všimněte si, že IP adresa, kterou jste obdrželi, je jedna z adres z rozsahu adres připojení typu Point-to-Site, který jste určili během vytváření vaší virtuální sítě. Výsledek by se měl podobat tomuto příkladu:

   ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
   ```

## <a name="connect-to-a-virtual-machine"></a>Připojení k virtuálnímu počítači

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add-or-remove-trusted-root-certificates"></a>Add or remove trusted root certificates

Důvěryhodný kořenový certifikát můžete do Azure přidat nebo ho z Azure odebrat. When you remove a root certificate, clients that have a certificate generated from that root can no longer authenticate and connect. For those clients to authenticate and connect again, you must install a new client certificate generated from a root certificate that's trusted by Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Přidání důvěryhodného kořenového certifikátu

Do Azure můžete přidat až 20 souborů .cer s důvěryhodnými kořenovými certifikáty. For instructions, see Upload the root certificate .cer file.

### <a name="to-remove-a-trusted-root-certificate"></a>Odebrání důvěryhodného kořenového certifikátu

1. On the **VPN connections** section of the page for your VNet, select the clients graphic to open the **Point-to-site VPN connection** page.

   ![Klienti](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. On the **Point-to-site VPN connection** page, select **Manage certificate** to open the **Certificates** page.

   ![Stránka certifikátů](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

3. On the **Certificates** page, select the ellipsis next to the certificate that you want to remove, then select **Delete**.

   ![Odstranění kořenového certifikátu](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)

## <a name="revoke-a-client-certificate"></a>Odvolání klientského certifikátu

If necessary, you can revoke a client certificate. Seznam odvolaných certifikátů umožňuje selektivně odepřít připojení Point-to-Site na základě jednotlivých klientských certifikátů. This method differs from removing a trusted root certificate. Pokud odeberete z Azure důvěryhodný kořenový certifikát v souboru .cer, dojde k odvolání přístupu pro všechny klientské certifikáty, které byly tímto odvolaným kořenovým certifikátem vygenerovány nebo podepsány. Odvolání klientského certifikátu namísto kořenového certifikátu umožňuje používat k ověřování pro připojení typu Point-to-Site další certifikáty vygenerované z kořenového certifikátu.

Běžnou praxí je použití kořenového certifikátu pro řízení přístupu na úrovni týmu nebo organizace, přičemž odvolání klientských certifikátů slouží pro detailní kontrolu přístupu jednotlivých uživatelů.

### <a name="to-revoke-a-client-certificate"></a>Odvolání klientského certifikátu

Klientský certifikát můžete odvolat tím, že přidáte jeho kryptografický otisk do seznamu odvolaných certifikátů.

1. Načtěte kryptografický otisk klientského certifikátu. Další informace najdete v tématu [Postup: Načtení kryptografického otisku certifikátu](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copy the information to a text editor and remove its spaces so that it's a continuous string.
3. Go to the classic virtual network. Select **Point-to-site VPN connection**, then select **Manage certificate** to open the **Certificates** page.
4. Select **Revocation list** to open the **Revocation list** page. 
5. Select **Add certificate** to open the **Add certificate to revocation list** page.
6. In **Thumbprint**, paste the certificate thumbprint as one continuous line of text, with no spaces. Select **OK** to finish.

Po dokončení aktualizace už nebude možné certifikát použít k připojení. Clients that try to connect by using this certificate receive a message saying that the certificate is no longer valid.

## <a name="point-to-site-faq"></a>Point-to-Site FAQ

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Další kroky

- After your connection is complete, you can add virtual machines to your virtual networks. Další informace najdete v tématu [Virtuální počítače](https://docs.microsoft.com/azure/). 

- To understand more about networking and Linux virtual machines, see [Azure and Linux VM network overview](../virtual-machines/linux/network-overview.md).

- Informace o odstraňování potíží s P2S najdete v článku [Poradce při potížích s připojeními Azure typu point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
