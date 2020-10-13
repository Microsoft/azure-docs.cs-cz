---
title: Průvodce nasazením FortiGate | Microsoft Docs
description: Nastavte a pracujte s produktem brány firewall nové generace Fortinet FortiGate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 357eb0a60e6246996de9ab75337ecc213d845ae7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91273326"
---
# <a name="fortigate-deployment-guide"></a>Průvodce nasazením FortiGate

Pomocí tohoto průvodce nasazením se dozvíte, jak nastavit a pracovat s produktem brány firewall nové generace Fortinet FortiGate.

## <a name="redeem-the-fortigate-license"></a>Uplatnit licenci FortiGate

Produkt Fortinet FortiGate Next-Generation firewall je k dispozici jako virtuální počítač v infrastruktuře Azure jako služba (IaaS). Pro tento virtuální počítač existují dva způsoby licencování: průběžné platby a vlastní licence.

Fortinet může poskytnout členům služby Azure Active Directory (Azure AD) možnost získat do provozu tým zabezpečeného přístupu k programu SHA (Secure Hybrid Access) s licencemi. V případech, kdy nebyla poskytnuta žádná licence, bude také možné použít nasazení s průběžnými platbami.

Pokud byla vydána licence, Fortinet poskytuje registrační kód, který je nutné znovu považovat za online.

![Snímek obrazovky s registračním kódem FortiGate SSL VPN](registration-code.png)

1. Zaregistrujte se na https://support.fortinet.com/ .
2. Po registraci se přihlaste https://support.fortinet.com/ .
3. Přejít na **Asset**  >  **Registration/Activate**.
4. Zadejte registrační kód, který poskytuje Fortinet.
5. Zadejte registrační kód, vyberte produkt, který **bude používat uživatel bez státní správy**, a vyberte **Další**.
6. Zadejte popis produktu (například Fortigate), nastavte partnera Fortinet jako **jinou**  >  **Společnost Microsoft**a vyberte **Další**.
7. Přijměte **smlouvu o registraci produktu Fortinet**a vyberte **Další**.
8. Přijměte **podmínky** a vyberte **Potvrdit**.
9. Vyberte **Stažení souboru s licencí**a pak licenci uložte pro pozdější verzi.


## <a name="download-firmware"></a>Stáhnout firmware

Virtuální počítač Azure Fortinet FortiGate aktuálně není dodáván s verzí firmwaru, která je nutná pro ověřování SAML. Nejnovější verzi je třeba získat z Fortinet.

1. Přihlaste se https://support.fortinet.com/ .
2. Přejít na **Stáhnout**  >  **Image firmwaru**.
3. Napravo od **poznámky k verzi**vyberte **Stáhnout**.
4. Vyberte **v6.** > **6.** > **6,4.**..
5. Stáhněte **FGT_VM64_AZURE-V6-build1637-Fortinet. out** výběrem odkazu **https** na stejném řádku.
6. Uložte soubor pro pozdější uložení.


## <a name="deploy-the-fortigate-vm"></a>Nasazení virtuálního počítače s FortiGate

1. Přejdete na adresu https://portal.azure.com a přihlaste se k předplatnému, do kterého chcete nasadit virtuální počítač s Fortigate.
2. Vytvořte novou skupinu prostředků nebo otevřete skupinu prostředků, do které chcete nasadit virtuální počítač s FortiGate.
3. Vyberte **Přidat**.
4. V **části Hledat na Marketplace**zadejte *Fort*. Vyberte **Fortinet FortiGate Next-Generation firewall**.
5. Vyberte plán softwaru (vlastní licence, pokud máte licenci nebo průběžné platby, pokud se nepoužívá). Vyberte **Vytvořit**.
6. Naplňte konfiguraci virtuálního počítače.

    ![Snímek obrazovky s vytvořením virtuálního počítače](virtual-machine.png)

7. Nastavte **typ ověřování** na **heslo**a zadejte přihlašovací údaje pro správu virtuálního počítače.
8. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**.
9. Počkejte, než se nasazení virtuálního počítače dokončí.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Nastavte statickou veřejnou IP adresu a přiřaďte plně kvalifikovaný název domény.

Pro konzistentní uživatelské prostředí nastavte veřejnou IP adresu přiřazenou k virtuálnímu počítači FortiGate, která se má staticky přiřadit. Kromě toho ho namapujte na plně kvalifikovaný název domény (FQDN).

1. Přejít na https://portal.azure.com a otevřít nastavení pro virtuální počítač Fortigate.
2. Na obrazovce **Přehled** vyberte veřejnou IP adresu.

    ![Snímek obrazovky s Fortigate SSL VPN.](public-ip-address.png)

3. Vyberte možnost **statické**  >  **uložení**.

Pokud vlastníte název veřejně směrovatelné domény pro prostředí, do kterého se virtuální počítač FortiGate nasazuje, vytvořte pro tento virtuální počítač záznam hostitele (A). Tento záznam je namapován na předchozí veřejnou IP adresu, která je staticky přiřazena.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>Vytvoří nové pravidlo skupiny zabezpečení příchozí sítě pro port TCP.

1. Přejít na https://portal.azure.com a otevřít nastavení pro virtuální počítač Fortigate.
2. V nabídce na levé straně vyberte **síť**. V seznamu je uvedené síťové rozhraní a zobrazí se pravidla pro příchozí porty.
3. Vyberte **Přidat pravidlo portu pro příchozí spojení**.
4. Vytvoří nové pravidlo příchozího portu pro TCP 8443.

    ![Snímek obrazovky s přidáním příchozího pravidla zabezpečení](port-rule.png)

5. Vyberte **Přidat**.


## <a name="create-a-custom-azure-app-for-fortigate"></a>Vytvoření vlastní aplikace Azure pro FortiGate

1. Přejít na https://portal.azure.com a otevřít podokno Azure AD pro tenanta, který bude poskytovat identitu pro přihlášení Fortigate.
2. V nabídce vlevo vyberte **podnikové aplikace**.
3. Vyberte možnost **Nová**aplikace  >  **mimo galerii**.
4. Zadejte název (například FortiGate) a vyberte **Přidat**.
5. V nabídce vlevo vyberte **Uživatelé a skupiny**.
6. Přidejte uživatele, kteří se budou moci přihlásit, a pak vyberte **přiřadit**.
7. V nabídce vlevo vyberte **jednotné přihlašování**.
8. Vyberte **SAML**.
9. V části **základní konfigurace SAML**vyberte ikonu tužky a upravte konfiguraci.
10. Nakonfigurujte toto:
    - **Identifikátor (ID entity)** , který má být `https://<address>/remote/saml/metadata` .
    - **Adresa URL odpovědi (adresa URL služby kontrolního výrazu)** , která má být  `https://<address>/remote/saml/login` .
    - **Odhlašovací adresa URL** `https://<address>/remote/saml/logout` .

    `<address>` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači FortiGate.

11. Každou z těchto adres URL si poznamenejte pro pozdější použití: ID entity, adresa URL odpovědi a adresa URL pro odhlášení.
12. Vyberte **Save (Uložit**) a zavřete **základní konfiguraci SAML**.
13. V části **3 – podpisový certifikát SAML**stáhněte **certifikát (Base64)** a uložte ho pro pozdější verzi.
14. V části **4 – nastavení (název aplikace)** zkopírujte adresu URL pro přihlášení k Azure, identifikátor Azure AD a adresu URL pro odhlášení Azure a uložte je pro pozdější použití.
15. V části **2 – atributy a deklarace uživatele**vyberte ikonu tužky a upravte konfiguraci.
16. Vyberte **Přidat novou deklaraci identity**a nastavte název na **uživatelské jméno**.
17. Nastavte zdrojový atribut na **User. userPrincipalName**.
18. Vyberte **Uložit**  >  **Přidat skupinu deklarací identity**  >  **všechny skupiny**.
19. Vyberte **přizpůsobit název deklarace skupiny**a nastavte název **skupiny**.
20. Vyberte **Uložit**.


## <a name="prepare-for-group-matching"></a>Příprava na spárování skupin

FortiGate v závislosti na členství ve skupině umožňuje různým funkcím uživatelského portálu po přihlášení. Může existovat například jedna z možností pro skupinu marketingu a další pro skupinu finance. Tady je postup, jak vytvořit skupiny pro uživatele:

1. Přejít na https://portal.azure.com a otevřít podokno Azure AD pro tenanta, který bude poskytovat identitu pro přihlášení Fortigate.
2. Vyberte **skupiny**  >  **Nová skupina**.
3. Vytvořte skupinu s následujícími podrobnostmi:
    - Typ skupiny = zabezpečení
    - Název skupiny = `a meaningful name`
    - Popis skupiny = `a meaningful description for the group`
    - Typ členství = přiřazeno
    - Členové = `users for the user experience that will map to this group`
4. Zopakujte kroky 3 a 4 pro všechny další uživatelské prostředí.
5. Po vytvoření skupin vyberte každou skupinu a zaznamenejte **ID objektu** pro každé z nich.
6. Tyto identifikátory objektů a názvy skupin uložte později.


## <a name="configure-the-fortigate-vm"></a>Konfigurace virtuálního počítače s FortiGate

Následující části vás seznámí s postupem nastavení virtuálního počítače FortiGate.

### <a name="install-the-license"></a>Nainstalovat licenci

1. Přejděte na `https://<address>`. Tady `<address>` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Pokračujte v předchozích chybách certifikátu.
3. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazování virtuálního počítače FortiGate.
4. Pokud nasazení používá model "Přineste si vlastní licenci", zobrazí se výzva k nahrání licence. Vyberte soubor s licencí, který jste vytvořili dříve, a nahrajte ho. Vyberte **OK** a restartujte virtuální počítač Fortigate.

    ![Snímek obrazovky s licencí virtuálního počítače FortiGate](license.png)

5. Po restartování se znovu přihlaste s přihlašovacími údaji správce a ověřte licenci.

### <a name="update-firmware"></a>Aktualizovat firmware

1. Přejděte na `https://<address>`. Tady `<address>` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Pokračujte v předchozích chybách certifikátu.
3. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazování virtuálního počítače FortiGate.
4. V nabídce vlevo vyberte **systémové**  >  **firmware**.
5. V části **Správa firmwaru**vyberte **Procházet**a vyberte soubor firmwaru stažený dříve.
6. Ignorujte upozornění a vyberte možnost **Konfigurace zálohování a upgrade**.

    ![Snímek obrazovky se správou firmwaru.](backup-configure-upgrade.png)

7. Vyberte **Pokračovat**.
8. Až budete vyzváni k uložení konfigurace FortiGate (jako soubor. conf), vyberte **Uložit**.
9. Počkejte, než se firmware nahraje a použije. Počkejte, až se virtuální počítač FortiGate restartuje.
10. Po restartování virtuálního počítače FortiGate se znovu přihlaste s přihlašovacími údaji správce.
11. Až budete vyzváni k nastavení řídicího panelu, vyberte **později**.
12. Po zahájení kurzu video vyberte **OK**.

### <a name="change-the-management-port-to-tcp"></a>Změna portu pro správu na TCP

1. Přejděte na `https://<address>`. Tady `<address>` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Pokračujte v předchozích chybách certifikátu.
3. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazování virtuálního počítače FortiGate.
4. V nabídce vlevo vyberte možnost **systém**.
5. V části **Nastavení správy**změňte port HTTPS na **8443**a vyberte **použít**.
6. Po použití změny se prohlížeč pokusí znovu načíst stránku pro správu, ale dojde k chybě. Od této chvíle je adresa stránky pro správu `https://<address>` .

    ![Snímek obrazovky nahrávání vzdáleného certifikátu](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Nahrajte podpisový certifikát SAML služby Azure AD.

1. Přejděte na `https://<address>`. Tady `<address>` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Pokračujte v předchozích chybách certifikátu.
3. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazování virtuálního počítače FortiGate.
4. V nabídce vlevo vyberte **systémové**  >  **certifikáty**.
5. Vyberte **importovat**  >  **vzdálený certifikát**.
6. Přejděte na certifikát stažený z nasazení vlastní aplikace FortiGate v tenantovi Azure. Vyberte ji a vyberte **OK**.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Nahrát a nakonfigurovat vlastní certifikát SSL

Můžete chtít nakonfigurovat virtuální počítač FortiGate s vlastním certifikátem SSL, který podporuje plně kvalifikovaný název domény, který používáte. Máte-li přístup k certifikátu protokolu SSL zabalenému pomocí privátního klíče ve formátu PFX, lze jej použít k tomuto účelu.

1. Přejděte na `https://<address>`. Tady `<address>` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Pokračujte v předchozích chybách certifikátu.
3. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazování virtuálního počítače FortiGate.
4. V nabídce vlevo vyberte **systémové**  >  **certifikáty**.
5. Vyberte **importovat**  >  certifikát**Local Certificate**  >  **PKCS #12**.
6. Přejděte k části. Soubor PFX, který obsahuje certifikát SSL a soukromý klíč.
7. Zadejte. Heslo PFX a smysluplný název certifikátu. Pak vyberte **OK**.
8. V nabídce vlevo vyberte **System**  >  **Nastavení**systému.
9. V části **Nastavení správy**rozbalte seznam vedle položky **certifikát serveru HTTPS**a vyberte certifikát SSL, který jste naimportovali dříve.
10. Vyberte **Použít**.
11. Zavřete okno prohlížeče a pokračujte na `https://<address>` .
12. Přihlaste se pomocí přihlašovacích údajů správce FortiGate. Nyní byste měli vidět správný certifikát SSL.


### <a name="perform-command-line-configuration"></a>Provést konfiguraci příkazového řádku

V následujících částech jsou uvedeny kroky pro různé konfigurace pomocí příkazového řádku.

#### <a name="for-saml-authentication"></a>Pro ověřování SAML

1. Přejít na https://portal.azure.com a otevřít nastavení pro virtuální počítač Fortigate.
2. V nabídce vlevo vyberte **sériové konzola**.
3. Přihlaste se ke konzole sériového portu pomocí pověření správce virtuálního počítače FortiGate. V dalším kroku jsou vyžadovány adresy URL, které jste si poznamenali dříve:
    - ID entity
    - Adresa URL odpovědi
    - Odhlašovací adresa URL
    - Přihlašovací adresa URL Azure
    - Identifikátor Azure AD
    - Adresa URL pro odhlášení Azure
4. V konzole sériového portu spusťte následující příkazy:

    ```
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert REMOTE_Cert_
    set user-name username
    set group-name group
    end
    ```
    > [!NOTE]
    > Adresa URL pro odhlášení Azure obsahuje `?` znak. K tomu je potřeba speciální klíčová sekvence, aby se mohla správně poskytnout konzole FortiGate Serial. Adresa URL je obvykle `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1` . Pokud to chcete uvést v konzole sériového portu, zadejte:
        ```
        set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
        ```.
    Potom zadejte CTRL + V a vložte zbytek adresy URL do části a dokončete řádek: 
        ```
        set idp-single-logout-url
        https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
        ```

5. Pro potvrzení konfigurace spusťte následující příkaz:

    ```
    show user saml
    ```

#### <a name="for-group-matching"></a>Pro spárování skupin

1. Přejít na https://portal.azure.com a otevřít nastavení pro virtuální počítač Fortigate.
2. V nabídce vlevo vyberte **sériové konzola**.
3. Přihlaste se ke konzole sériového portu pomocí pověření správce virtuálního počítače FortiGate.
4. V konzole sériového portu spusťte následující příkazy:

    ```
    config user group
    edit <group 1 name>
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <group 1 Object Id>
    next
    end
    next
    ```

    Pro každou další skupinu, která bude mít v FortiGate různé možnosti portálu, opakujte tyto příkazy (počínaje druhým řádkem kódu).

#### <a name="for-authentication-timeout"></a>Pro časový limit ověřování

1. Přejít na https://portal.azure.com a otevřít nastavení pro virtuální počítač Fortigate.
2. V nabídce vlevo vyberte **sériové konzola**.
3. Přihlaste se ke konzole sériového portu pomocí pověření správce virtuálního počítače FortiGate.
4. V konzole sériového portu spusťte následující příkazy:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>Vytvoření portálů a zásad brány firewall sítě VPN

1. Přejděte na `https://<address>`. Tady `<address>` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazování virtuálního počítače FortiGate.
3. V levé nabídce vyberte **VPN**  >  **SSL-portály VPN**  >  **vytvořit nové**.
6. Zadejte název (obvykle se shoduje se skupinou Azure, která se používá k poskytování vlastního prostředí portálu).
7. Vyberte znaménko plus ( **+** ) vedle **zdrojové fondy IP adres**, vyberte výchozí fond a pak vyberte **Zavřít**.
8. Přizpůsobte si prostředí pro tuto skupinu. Pro účely testování jde o přizpůsobení zprávy portálu a motivu. Můžete tak také vytvořit vlastní záložky, které uživatele přesměrují na interní prostředky.
9. Vyberte **OK**.
10. Opakujte kroky 5-9 pro každou skupinu Azure, která bude mít vlastní prostředí portálu.
11. V části VPN vyberte **Nastavení SSL-VPN**.
12. Vyberte znaménko plus ( **+** ) vedle pole **naslouchat rozhraním**, vyberte **PORT1**a pak vyberte **Zavřít**.
14. Pokud jste dřív nainstalovali vlastní certifikát SSL, změňte **certifikát serveru** tak, aby používal vlastní certifikát SSL v rozevírací nabídce.
15. V části **ověřování/mapování portálu**vyberte **vytvořit novou**. Vyberte první skupinu Azure a porovnejte ji s portálem se stejným názvem. Pak vyberte **OK**.
18. Opakujte kroky 15-17 pro každé párování skupiny Azure a portálu.
19. V části **ověřování/mapování portálu**upravte **všechny ostatní uživatele nebo skupiny**.
20. Nastavte na portálu **úplný přístup**a vyberte **OK**  >  **použít**.
23. Posuňte se na začátek stránky **Nastavení SSL-VPN** a vyberte **neexistují žádné zásady SSL-VPN. Kliknutím sem vytvoříte nové zásady SSL-VPN pomocí těchto nastavení.**
24. Zadejte název, třeba **VPN GRP**. Pak nastavte **odchozí rozhraní** na **port**a vyberte **zdroj**.
27. V části **adresa**vyberte **vše**.
28. V části **uživatel**vyberte první skupinu Azure.
29. Vyberte **Zavřít**  >  **cíl**. V části **adresa**se obvykle jedná o interní síť. Vyberte **Login.Microsoft.com** pro testování.
32. Vyberte **Zavřít**  >  **službu**  >  **vše**. Pak vyberte **Zavřít**  >  **OK**.
37. V nabídce vlevo vyberte **zásady & objekty**  >  **zásady brány firewall**.
39. Rozbalte **port SSL-VPN Tunneling Interface (SSL. root)**  >  **port**.
40. Klikněte pravým tlačítkem na zásadu sítě VPN vytvořenou dříve (**VPN GRP 1**) a vyberte **Kopírovat**.
41. Klikněte pravým tlačítkem na zásady sítě VPN a vyberte **Vložit**  >  **níže**.
42. Upravte nové zásady a poskytněte jí jiný název (například **VPN Grp2**). Změňte také skupinu, na kterou se vztahuje (na jinou skupinu Azure).
43. Klikněte pravým tlačítkem na novou zásadu a nastavte stav **povoleno**.


## <a name="test-sign-in-by-using-azure"></a>Testování přihlášení pomocí Azure

1. Pomocí privátní relace prohlížeče, přejít na `https://<address>` .  
2. Přihlášení by se mělo přesměrovat do Azure AD, aby se přihlásilo.
3. Po zadání přihlašovacích údajů pro uživatele, který je přiřazený k aplikaci FortiGate v tenantovi Azure, by se měl zobrazit příslušný uživatelský portál.

    ![Snímek obrazovky s FortiGate SSL VPN](test-sign-in.png)
