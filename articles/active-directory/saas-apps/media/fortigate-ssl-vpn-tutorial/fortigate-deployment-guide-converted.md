---
title: Průvodce nasazením FortiGate | Microsoft Docs
description: Nastavte a pracujte s produktem brány firewall nové generace Fortinet FortiGate.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: jeedes
ms.openlocfilehash: cdaa6a9601452100ab90ef8b0f2191002f256b74
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025427"
---
# <a name="fortigate-azure-virtual-machine-deployment-guide"></a>Průvodce nasazením virtuálního počítače Azure s FortiGate

Pomocí této příručky pro nasazení se dozvíte, jak nastavit a pracovat s produktem brány firewall nové generace Fortinet FortiGate nasazeným jako virtuální počítač Azure. Kromě toho nakonfigurujete aplikaci Galerie Azure AD FortiGate SSL VPN tak, aby poskytovala ověřování pomocí sítě VPN prostřednictvím Azure Active Directory.

## <a name="redeem-the-fortigate-license"></a>Uplatnit licenci FortiGate

Produkt Fortinet FortiGate Next-Generation firewall je k dispozici jako virtuální počítač v infrastruktuře Azure jako služba (IaaS). Pro tento virtuální počítač existují dva způsoby licencování: průběžné platby a používání vlastní licence (BYOL).

Pokud jste si zakoupili licenci FortiGate z Fortinet pro použití s možností nasazení virtuálního počítače BYOL, uplatněte ji ze stránky aktivace produktu Fortinet – https://support.fortinet.com . Výsledný soubor s licencí bude mít příponu. lic.

## <a name="download-firmware"></a>Stáhnout firmware

V době psaní se virtuální počítač Azure Fortinet FortiGate nedodává s verzí firmwaru, která je potřebná pro ověřování SAML. Nejnovější verzi je třeba získat z Fortinet.

1. Přihlaste se https://support.fortinet.com/ .
2. Přejít na **Stáhnout**  >  **Image firmwaru**.
3. Napravo od **poznámky k verzi** vyberte **Stáhnout**.
4. Vyberte **v 6.00**  >  **6,4**  >  **6.4.2**.
5. Stáhněte **FGT_VM64_AZURE-V6-build1723-Fortinet. out** výběrem odkazu **https** na stejném řádku.
6. Uložte soubor pro pozdější uložení.

## <a name="deploy-the-fortigate-vm"></a>Nasazení virtuálního počítače s FortiGate

1. Přejdete na Azure Portal a přihlaste se k předplatnému, do kterého nasadíte virtuální počítač FortiGate.
2. Vytvořte novou skupinu prostředků nebo otevřete skupinu prostředků, do které budete nasazovat virtuální počítač s FortiGate.
3. Vyberte **Přidat**.
4. V **části Hledat na Marketplace** zadejte *Fort*. Vyberte **Fortinet FortiGate Next-Generation firewall**.
5. Vyberte plán softwaru (vlastní licence, pokud máte licenci nebo průběžné platby, pokud se nepoužívá). Vyberte **Vytvořit**.
6. Naplňte konfiguraci virtuálního počítače.

    ![Snímek obrazovky s vytvořením virtuálního počítače](virtual-machine.png)

7. Nastavte **typ ověřování** na **heslo** a zadejte přihlašovací údaje pro správu virtuálního počítače.
8. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**.
9. Počkejte, než se nasazení virtuálního počítače dokončí.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Nastavte statickou veřejnou IP adresu a přiřaďte plně kvalifikovaný název domény.

Pro konzistentní uživatelské prostředí nastavte veřejnou IP adresu přiřazenou k virtuálnímu počítači FortiGate, která se má staticky přiřadit. Kromě toho ho namapujte na plně kvalifikovaný název domény (FQDN).

1. Přejít na Azure Portal a otevřete nastavení pro virtuální počítač FortiGate.
2. Na obrazovce **Přehled** vyberte veřejnou IP adresu.

    ![Snímek obrazovky s Fortigate SSL VPN.](public-ip-address.png)

3. Vyberte možnost **statické**  >  **uložení**.

Pokud vlastníte název veřejně směrovatelné domény pro prostředí, do kterého se virtuální počítač FortiGate nasazuje, vytvořte pro tento virtuální počítač záznam hostitele (A). Tento záznam je namapován na předchozí veřejnou IP adresu, která je staticky přiřazena.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port-8443"></a>Vytvoří nové pravidlo skupiny zabezpečení příchozí sítě pro port TCP 8443.

1. Přejít na Azure Portal a otevřete nastavení pro virtuální počítač FortiGate.
2. V nabídce na levé straně vyberte **síť**. V seznamu je uvedené síťové rozhraní a zobrazí se pravidla pro příchozí porty.
3. Vyberte **Přidat pravidlo portu pro příchozí spojení**.
4. Vytvoří nové pravidlo příchozího portu pro TCP 8443.

    ![Snímek obrazovky s přidáním příchozího pravidla zabezpečení](port-rule.png)

5. Vyberte **Přidat**.

## <a name="create-a-second-virtual-nic-for-the-vm"></a>Vytvoření druhé virtuální síťové karty pro virtuální počítač

Aby byly k dispozici interní prostředky pro uživatele, musí být do virtuálního počítače FortiGate přidána druhá virtuální síťová karta. Virtual Network v Azure, na které se nachází virtuální síťová karta, musí mít směrovatelné připojení k těmto interním prostředkům.

1. Přejít na Azure Portal a otevřete nastavení pro virtuální počítač FortiGate.
2. Pokud virtuální počítač FortiGate ještě není zastavený, vyberte **zastavit** a počkejte, než se virtuální počítač vypne.
3. V nabídce na levé straně vyberte **síť**.
4. Vyberte **připojit síťové rozhraní**.
5. Vyberte **vytvořit a připojit síťové rozhraní**.
6. Nakonfigurujte vlastnosti nového síťového rozhraní a pak vyberte **vytvořit**.

    ![Snímek obrazovky s vytvořením síťového rozhraní](new-network-interface.png)

7. Spusťte virtuální počítač FortiGate.


## <a name="configure-the-fortigate-vm"></a>Konfigurace virtuálního počítače s FortiGate

Následující části vás seznámí s postupem nastavení virtuálního počítače FortiGate.

### <a name="install-the-license"></a>Nainstalovat licenci

1. Přejděte na `https://<address>`. Tady `<address>` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Pokračujte v předchozích chybách certifikátu.
3. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazování virtuálního počítače FortiGate.
4. Pokud nasazení používá model "Přineste si vlastní licenci", zobrazí se výzva k nahrání licence. Vyberte soubor s licencí, který jste vytvořili dříve, a nahrajte ho. Vyberte **OK** a restartujte virtuální počítač Fortigate.

    ![Snímek obrazovky s licencí virtuálního počítače FortiGate](license.png)

5. Po restartování se znovu přihlaste s přihlašovacími údaji správce a ověřte licenci.

### <a name="update-firmware"></a>Aktualizace firmwaru

1. Přejděte na `https://<address>`. Tady `<address>` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Pokračujte v předchozích chybách certifikátu.
3. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazování virtuálního počítače FortiGate.
4. V nabídce vlevo vyberte **systémové**  >  **firmware**.
5. V části **Správa firmwaru** vyberte **Procházet** a vyberte soubor firmwaru stažený dříve.
6. Ignorujte upozornění a vyberte možnost **Konfigurace zálohování a upgrade**.

    ![Snímek obrazovky se správou firmwaru.](backup-configure-upgrade.png)

7. Vyberte **Pokračovat**.
8. Až budete vyzváni k uložení konfigurace FortiGate (jako soubor. conf), vyberte **Uložit**.
9. Počkejte, než se firmware nahraje a použije. Počkejte, až se virtuální počítač FortiGate restartuje.
10. Po restartování virtuálního počítače FortiGate se znovu přihlaste s přihlašovacími údaji správce.
11. Až budete vyzváni k nastavení řídicího panelu, vyberte **později**.
12. Po zahájení kurzu video vyberte **OK**.

### <a name="change-the-management-port-to-tcp-8443"></a>Změna portu pro správu na TCP 8443

1. Přejděte na `https://<address>`. Tady `<address>` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Pokračujte v předchozích chybách certifikátu.
3. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazování virtuálního počítače FortiGate.
4. V nabídce vlevo vyberte možnost **systém**.
5. V části **Nastavení správy** změňte port HTTPS na **8443** a vyberte **použít**.
6. Po použití změny se prohlížeč pokusí znovu načíst stránku pro správu, ale dojde k chybě. Od této chvíle je adresa stránky pro správu `https://<address>:8443` .

    ![Snímek obrazovky nahrávání vzdáleného certifikátu](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Nahrajte podpisový certifikát SAML služby Azure AD.

1. Přejděte na `https://<address>:8443`. Tady `<address>` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Pokračujte v předchozích chybách certifikátu.
3. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazování virtuálního počítače FortiGate.
4. V nabídce vlevo vyberte **systémové**  >  **certifikáty**.
5. Vyberte **importovat**  >  **vzdálený certifikát**.
6. Přejděte na certifikát stažený z nasazení vlastní aplikace FortiGate v tenantovi Azure. Vyberte ji a vyberte **OK**.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Nahrát a nakonfigurovat vlastní certifikát SSL

Můžete chtít nakonfigurovat virtuální počítač FortiGate s vlastním certifikátem SSL, který podporuje plně kvalifikovaný název domény, který používáte. Máte-li přístup k certifikátu protokolu SSL zabalenému pomocí privátního klíče ve formátu PFX, lze jej použít k tomuto účelu.

1. Přejděte na `https://<address>:8443`. Tady `<address>` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Pokračujte v předchozích chybách certifikátu.
3. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazování virtuálního počítače FortiGate.
4. V nabídce vlevo vyberte **systémové**  >  **certifikáty**.
5. Vyberte **importovat**  >  certifikát **Local Certificate**  >  **PKCS #12**.
6. Přejděte k části. Soubor PFX, který obsahuje certifikát SSL a soukromý klíč.
7. Zadejte. Heslo PFX a smysluplný název certifikátu. Pak vyberte **OK**.
8. V nabídce vlevo vyberte   >  **Nastavení** systému.
9. V části **Nastavení správy** rozbalte seznam vedle položky **certifikát serveru HTTPS** a vyberte certifikát SSL, který jste naimportovali dříve.
10. Vyberte **Použít**.
11. Zavřete okno prohlížeče a pokračujte na `https://<address>:8443` .
12. Přihlaste se pomocí přihlašovacích údajů správce FortiGate. Nyní byste měli vidět správný certifikát SSL.

### <a name="configure-authentication-timeout"></a>Konfigurace časového limitu ověřování

1. Přejít na Azure Portal a otevřete nastavení pro virtuální počítač FortiGate.
2. V nabídce vlevo vyberte **sériové konzola**.
3. Přihlaste se ke konzole sériového portu pomocí pověření správce virtuálního počítače FortiGate.
4. V konzole sériového portu spusťte následující příkazy:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```

### <a name="ensure-network-interfaces-are-obtaining-ip-addresses"></a>Zajistěte, aby síťová rozhraní získala IP adresy.

1. Přejděte na `https://<address>:8443`. Tady `<address>` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazování virtuálního počítače FortiGate.
3. V nabídce na levé straně vyberte **síť**.
4. V části síť vyberte **rozhraní**.
5. Zkontrolujte PORT1 (externí rozhraní) a Port2 (interní rozhraní), abyste zajistili, že získají IP adresu ze správné podsítě Azure.
    a. Pokud některý z portů nezíská IP adresu z podsítě (přes protokol DHCP), klikněte pravým tlačítkem na port a vyberte **Upravit**.
    b. V poli režim adresování se ujistěte, že je vybraná možnost **DHCP** .
    c. Seelct **OK**.

    ![Snímek obrazovky adresování síťového rozhraní.](addressing.png)

### <a name="ensure-fortigate-vm-has-correct-route-to-on-premises-corporate-resources"></a>Ujistěte se, že virtuální počítač FortiGate má správnou trasu k místním podnikovým prostředkům.

Virtuální počítače Azure s více adresami mají všechna síťová rozhraní ve stejné virtuální síti (ale pravděpodobně samostatné podsítě). To často znamená, že obě síťová rozhraní mají připojení k místním podnikovým prostředkům, které jsou publikovány prostřednictvím FortiGate. Z tohoto důvodu je potřeba vytvořit vlastní položky tras, které zajistí, že se provoz ukončí ze správného rozhraní, když se vytvoří požadavky na místní podnikové prostředky.

1. Přejděte na `https://<address>:8443`. Tady `<address>` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazování virtuálního počítače FortiGate.
3. V nabídce na levé straně vyberte **síť**.
4. V části síť vyberte **statické trasy**.
5. Vyberte **Vytvořit nový**.
6. Vedle pole cíl vyberte **podsíť**.
7. V části podsíť zadejte informace o podsíti, ve kterých se nacházejí místní podnikové prostředky (například 10.1.0.0/255.255.255.0).
8. Vedle pole Adresa brány zadejte bránu v podsíti Azure, kde je připojen PORT2 (například to obvykle končí na 1 jako 10.6.1.1).
9. Vedle pole rozhraní vyberte interní síťové rozhraní, PORT2
10. Vyberte **OK**.

    ![Snímek obrazovky s konfigurací trasy.](route.png)

## <a name="configure-fortigate-ssl-vpn"></a>Konfigurace FortiGate SSL VPN

Postupujte podle kroků popsaných v části https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial
