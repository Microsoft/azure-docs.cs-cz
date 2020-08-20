---
title: Průvodce nasazením FortiGate | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a FortiGate SSL VPN.
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
ms.openlocfilehash: 999e19ffad1d18e163881c844cbf30f8b7fef574
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88658410"
---
# <a name="fortigate-deployment-guide"></a>Průvodce nasazením FortiGate

## <a name="contents"></a>Obsah

- Uplatnění licence FortiGate
- Stáhnout firmware
- Nasazení virtuálního počítače s FortiGate
   - Nastavte veřejnou IP adresu Statuc a přiřaďte plně kvalifikovaný název domény.
   - Vytvoří nové pravidlo skupiny zabezpečení příchozí sítě pro port TCP.
- Vytvoření vlastní aplikace Azure pro FortiGate
- Příprava na spárování skupin
   - Vytváření skupin pro uživatele
- Konfigurace virtuálního počítače s FortiGate
   - Nainstalovat licenci
   - Aktualizace firmwaru
   - Změna portu pro správu na TCP
   - Nahrajte Azure Active Directory podpisový certifikát SAML.
   - Nahrát a nakonfigurovat vlastní certifikát SSL
   - Provést konfiguraci příkazového řádku
   - Vytvoření portálů a zásad brány firewall sítě VPN
- Testování přihlášení pomocí Azure

## <a name="redeeming-the-fortigate-license"></a>Uplatnění licence FortiGate

Produkt Fortinet FortiGate Next-Generation firewall je k dispozici jako virtuální počítač v Azure IaaS. Pro tento virtuální počítač existují dva způsoby licencování –

- Průběžné platby (PAYG)
- Přineste si vlastní licenci (BYOL)

I když partnerství s Fortinet poskytuje pokyny k zabezpečenému hybridnímu přístupu (SHA), Fortinet může poskytnout členům Azure AD přístup k produkčnímu týmu SHA s licencemi. V případech, kdy nebyla poskytnuta žádná licence, bude PAYG nasazení fungovat také.

V případech, kdy se licence vystavila, Fortinet poskytuje registrační kód, který je nutné znovu považovat za online.

![Fortigate SSL VPN](registration-code.png)

1. Zaregistrovat na https://support.fortinet.com/
2. Po registraci se přihlaste na https://support.fortinet.com/
3. Přejít na **Asset** – > **Registrovat/aktivovat**
4. Zadejte registrační kód, který poskytuje Fortinet
5. Zadejte registrační kód, vyberte produkt, který **bude používat uživatel bez státní správy** , a klikněte na **Další** .
6. Zadejte popis produktu (např. FortiGate), nastavte partnera Fortinet jako **jinou** > **Microsoft** a klikněte na **Další** .
7. Přijměte **smlouvu o registraci produktu Fortinet** a klikněte na **Další** .
8. Přijměte **podmínky** a klikněte na **Potvrdit** .
9. Klikněte na **soubor s licencí stáhnout** a pak licenci uložte pro pozdější verzi.


## <a name="download-firmware"></a>Stáhnout firmware

V době psaní se virtuální počítač Azure Fortinet FortiGate nedodává s verzí firmwaru, která je potřebná pro ověřování SAML. Nejnovější verzi je třeba získat z Fortinet.

1. Přihlášení na https://support.fortinet.com/
2. Přejít na **Image firmwaru** pro **stažení** >
3. Klikněte na **Stáhnout** vpravo od **poznámky k verzi** .
4. Klikněte na **v6.**
5. Klikněte na **6.**
6. Klikněte na **6,4.**
7. Stáhněte **FGT_VM64_AZURE-V6-build1637-Fortinet. out** kliknutím na odkaz **https** na stejném řádku.
8. Uložte soubor pro pozdější


## <a name="deploy-the-fortigate-vm"></a>Nasazení virtuálního počítače s FortiGate

1. Přejděte na https://portal.azure.com odběr a přihlaste se k předplatnému, do kterého chcete nasadit virtuální počítač s Fortigate.
2. Vytvořte novou skupinu prostředků nebo otevřete skupinu prostředků, do které chcete nasadit virtuální počítač s FortiGate.
3. Klikněte na **Přidat** .
4. Do dialogového okna **Hledat v Marketplace** zadejte "Forti" a vyberte **Fortinet FortiGate Next-** **Generation firewall** .
5. Vyberte plán softwaru (BYOL Pokud máte licenci nebo PAYG, pokud není), a klikněte na **vytvořit** .
6. Naplnit konfiguraci virtuálního počítače

    ![Fortigate SSL VPN](virtual-machine.png)

7. Nastavte typ ověřování na **heslo** a poskytněte přihlašovací údaje pro správu virtuálního počítače.
8. Klikněte na tlačítko **zkontrolovat + vytvořit** .
9. Klikněte na **Vytvořit**.
10. Počkejte, až se nasazení virtuálního počítače dokončí.


### <a name="set-a-statuc-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Nastavte veřejnou IP adresu Statuc a přiřaďte plně kvalifikovaný název domény.

Pro konzistentní činnost koncového uživatele je žádoucí nastavit veřejnou přiřazení veřejné IP adresy přiřazené k virtuálnímu počítači FortiGate. Mapování na plně kvalifikovaný název domény je také užitečné ze stejných důvodů.

_Nastavení statické veřejné IP adresy_

1. Přejděte na adresu https://portal.azure.com a otevřete nastavení pro virtuální počítač Fortigate.
2. Na obrazovce **Přehled** klikněte na veřejnou IP adresu.

    ![Fortigate SSL VPN](public-ip-address.png)

3. Klikněte na **statická** a pak klikněte na **Uložit** .

_Přiřazení plně kvalifikovaného názvu domény_

Pokud vlastníte název veřejně směrovatelné domény pro prostředí, do kterého se virtuální počítač FortiGate nasazuje, vytvořte pro virtuální počítač záznam hostitele (A), který se mapuje na veřejnou IP adresu, která je staticky přiřazená výše.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>Vytvoří nové pravidlo skupiny zabezpečení příchozí sítě pro port TCP.

1. Přejděte na adresu https://portal.azure.com a otevřete nastavení pro virtuální počítač Fortigate.
2. V nabídce na levé straně klikněte na **sítě** . Bude uvedeno síťové rozhraní a zobrazí se pravidla pro příchozí porty.
3. Klikněte na **Přidat pravidlo portu pro příchozí spojení** .
4. Vytvoří nové pravidlo příchozího portu pro TCP 8443.

    ![Fortigate SSL VPN](port-rule.png)

5. Klikněte na **Přidat** .


## <a name="create-a-custom-azure-app-for-fortigate"></a>Vytvoření vlastní aplikace Azure pro FortiGate

1. Přejděte na adresu https://portal.azure.com a otevřete okno Azure Active Directory pro tenanta, který bude poskytovat identitu pro přihlášení Fortigate.
2. V nabídce na levé straně klikněte na **podnikové aplikace** .
3. Klikněte na **Nová aplikace** .
4. Klikněte na **aplikaci mimo galerii** .
5. Zadejte název (třeba FortiGate) a klikněte na **Přidat** .
6. V nabídce na levé straně klikněte na **Uživatelé a skupiny** .
7. Přidejte uživatele, kteří se budou moci přihlásit, a klikněte na **přiřadit** .
8. V nabídce na levé straně klikněte na **jednotné přihlašování** .
9. Klikněte na **SAML** .
10. V části **základní konfigurace SAML** klikněte na tužku a upravte konfiguraci.
11. Konfigurace
    - Identifikátor (ID entity), který se má `https://<address>/remote/saml/metadata`
    - Adresa URL odpovědi (adresa URL služby kontrolního výrazu), která se má  `https://<address>/remote/saml/login`
    - Odhlašovací URL, která se má `https://<address>/remote/saml/logout`

    Kde `address` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

    Každou z těchto adres URL zaznamenejte pro pozdější použití –

    - ID entity
    - Adresa URL odpovědi
    - Odhlašovací adresa URL
12. Klikněte na **Uložit**.
13. Zavřít základní konfiguraci SAML
14. V části **3 – podpisový certifikát SAML** stáhněte **certifikát (Base64)** a uložte ho pro pozdější verzi.
15. V části **4 – nastavení (název aplikace)** , kopírování přihlašovací adresy URL Azure, identifikátoru Azure AD a adresy URL pro odhlášení Azure a jejich uložení pro pozdější použití
    - Přihlašovací adresa URL Azure
    - Identifikátor Azure AD
    - Adresa URL pro odhlášení Azure
16. V části **2 – atributy a deklarace uživatele** klikněte na tužku a upravte konfiguraci.
17. Klikněte na **Přidat novou deklaraci identity** .
18. Nastavte název na **uživatelské jméno** .
19. Nastavte zdrojový atribut na **User. userPrincipalName.**
20. Klikněte na **Uložit**.
21. Klikněte na **přidat deklaraci skupiny** .
22. Vybrat **všechny skupiny**
23. Ověřte **přizpůsobení názvu deklarace identity skupiny** .
24. Nastavit název na **skupinu**
25. Klikněte na **Uložit**.


## <a name="prepare-for-group-matching"></a>Příprava na spárování skupin

FortiGate umožňuje, aby po přihlášení na základě členství ve skupinách byly různé možnosti uživatelského portálu. Například může existovat jedna z možností pro skupinu marketingu a další pro skupinu finance.

Následujícím způsobem proveďte následující konfiguraci –

### <a name="create-groups-for-users"></a>Vytváření skupin pro uživatele

1. Přejděte na adresu https://portal.azure.com a otevřete okno Azure Active Directory pro tenanta, který bude poskytovat identitu pro přihlášení Fortigate.
2. Klikněte na **skupiny** .
3. Klikněte na **Nová skupina** .
4. Vytvořit skupinu pomocí
    - Typ skupiny = zabezpečení
    - Název skupiny = `a meaningful name`
    - Popis skupiny = `a meaningful description for the group`
    - Typ členství = přiřazeno
    - Členové = `users for the user experience that will map to this group`
5. Zopakujte kroky 3 a 4 pro všechny další uživatelské prostředí.
6. Po vytvoření skupin vyberte každou skupinu a zaznamenejte ID objektu pro každé z nich.
7. Uložit tato ID objektů a názvy skupin pro pozdější


## <a name="configure-the-fortigate-vm"></a>Konfigurace virtuálního počítače s FortiGate

### <a name="install-the-license"></a>Nainstalovat licenci

1. Přejděte na adresu `https://<address>`.

    Tady `address` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Pokračovat v předchozích chybách certifikátu
3. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazení virtuálního počítače FortiGate.
4. Pokud nasazení používá model BYOL, zobrazí se výzva k nahrání licence. Vyberte soubor s licencí, který jste vytvořili dříve, a nahrajte ho, klikněte na **OK** a restartujte virtuální počítač Fortigate –

    ![Fortigate SSL VPN](license.png)

5. Po restartování se znovu přihlaste s přihlašovacími údaji správce a ověřte licenci.

### <a name="update-firmware"></a>Aktualizace firmwaru

1. Přejděte na adresu `https://<address>`.

    Tady `address` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Pokračovat v předchozích chybách certifikátu
3. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazení virtuálního počítače FortiGate.
4. V nabídce na levé straně klikněte na **systém** .
5. V nabídce na levé straně v části systém klikněte na **firmware** .
6. Na stránce Správa firmwaru klikněte na **Procházet** a vyberte soubor firmwaru stažený dříve.
7. Ignorovat upozornění a kliknout na **zálohování konfigurace a upgrade** –

    ![Fortigate SSL VPN](backup-configure-upgrade.png)

8. Klikněte na **pokračovat** .
9. Po zobrazení výzvy k uložení konfigurace FortiGate (jako souboru. conf) klikněte na **Uložit** .
10. Počkejte, než se firmware nahraje, aby se mohl použít a aby se virtuální počítač FortiGate restartoval.
11. Po restartování virtuálního počítače FortiGate se znovu přihlaste s přihlašovacími údaji správce.
12. Po zobrazení výzvy k provedení nastavení řídicího panelu klikněte na tlačítko **Další** .
13. Po zahájení kurzu videa klikněte na **OK** .

### <a name="change-the-management-port-to-tcp"></a>Změna portu pro správu na TCP

1. Přejděte na adresu `https://<address>`.

    Tady `address` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Pokračovat v předchozích chybách certifikátu
3. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazení virtuálního počítače FortiGate.
4. V nabídce na levé straně klikněte na **systém** .
5. V části nastavení správy změňte port HTTPS na **8443** .
6. Klikněte na **použít** .
7. Po použití změny se prohlížeč pokusí znovu načíst stránku pro správu, ale dojde k chybě. Od této chvíle bude adresa stránky pro správu `https://<address>`

    ![Fortigate SSL VPN](certificate.png)

### <a name="upload-the-azure-active-directory-saml-signing-certificate"></a>Nahrajte Azure Active Directory podpisový certifikát SAML.

1. Přejděte na adresu `https://<address>`.

    Tady `address` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Pokračovat v předchozích chybách certifikátu
3. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazení virtuálního počítače FortiGate.
4. V nabídce na levé straně klikněte na **systém** .
5. V části systém klikněte na **certifikáty** .
6. Klikněte na **Import** -> **vzdálený certifikát** .
7. Přejděte na certifikát stažený z nasazení vlastní aplikace FortiGate v tenantovi Azure, vyberte ho a klikněte na **OK** .

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Nahrát a nakonfigurovat vlastní certifikát SSL

Můžete chtít nakonfigurovat virtuální počítač FortiGate s vlastním certifikátem SSL, který podporuje plně kvalifikovaný název domény, který používáte. Máte-li přístup k certifikátu protokolu SSL zabalenému pomocí privátního klíče v nástroji. Formát PFX, který se dá použít k tomuto účelu

1. Přejděte na adresu `https://<address>`.

    Tady `address` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Pokračovat v předchozích chybách certifikátu
3. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazení virtuálního počítače FortiGate.
4. V nabídce na levé straně klikněte na **systém** .
5. V části systém klikněte na **certifikáty** .
6. Klikněte na **Import** -> **místní certifikát** .
7. Kliknout na **PKCS #12 certifikát**
8. Přejděte k části. Soubor PFX obsahující certifikát SSL a privátní klíč
9. Zadejte. Heslo PFX
10. Zadejte smysluplný název certifikátu.
11. Klikněte na tlačítko **OK**.
12. V nabídce na levé straně klikněte na **systém** .
13. V části systém klikněte na **Nastavení** .
14. V části nastavení správy rozbalte rozevírací seznam vedle položky certifikát serveru HTTPS a vyberte certifikát SSL importovaný výše.
15. Klikněte na **použít** .
16. Zavřete okno prohlížeče a pak znovu přejděte na `https://<address>`
17. Přihlaste se pomocí přihlašovacích údajů správce FortiGate a sledujte správný používaný certifikát SSL.


### <a name="perform-command-line-configuration"></a>Provést konfiguraci příkazového řádku

_Provést konfiguraci příkazového řádku pro ověřování SAML_

1. Přejděte na adresu https://portal.azure.com a otevřete nastavení pro virtuální počítač Fortigate.
2. V nabídce na levé straně klikněte na **sériová konzola** .
3. Přihlaste se v konzole sériového portu pomocí přihlašovacích údajů správce virtuálních počítačů FortiGate.

    V dalším kroku se budou vyžadovat adresy URL, které jste nahráli dříve. Ty

    - ID entity
    - Adresa URL odpovědi
    - Odhlašovací adresa URL
    - Přihlašovací adresa URL Azure
    - Identifikátor Azure AD
    - Adresa URL pro odhlášení Azure
4. V konzole sériového portu spusťte následující příkazy –

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
    > Adresa URL pro odhlášení Azure obsahuje? optické. K tomu je potřeba speciální klíčová sekvence, aby se mohla správně poskytnout konzole FortiGate Serial. Adresa URL je obvykle –

    `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`

    Pokud to chcete v konzole sériového rozhraní poskytnout, pokračujte zadáním

    ```
    set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
    ```
    Pak zadejte CTRL + V,

    Pak vložte zbytek adresy URL do a dokončete řádek.

    ```
    set idp-single-logout-url
    https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
    ```

5. Ověření konfigurace, Execute –

    ```
    show user saml
    ```

_Provést konfiguraci příkazového řádku pro spárování skupin_

1. Přejděte na adresu https://portal.azure.com a otevřete nastavení pro virtuální počítač Fortigate.
2. V nabídce na levé straně klikněte na **sériová konzola** .
3. Přihlaste se v konzole sériového portu pomocí přihlašovacích údajů správce virtuálních počítačů FortiGate.
4. V konzole sériového portu spusťte následující příkazy –

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

    Tento příkaz opakujte z úpravy `group 1 name` , ale pro každou další skupinu, která bude mít v Fortigate různé možnosti portálu.

_Provést konfiguraci příkazového řádku pro časový limit ověřování_

1. Přejděte na adresu https://portal.azure.com a otevřete nastavení pro virtuální počítač Fortigate.
2. V nabídce na levé straně klikněte na **sériová konzola** .
3. Přihlaste se v konzole sériového portu pomocí přihlašovacích údajů správce virtuálních počítačů FortiGate.
4. V konzole sériového portu spusťte následující příkazy –

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>Vytvoření portálů a zásad brány firewall sítě VPN

1. Přejděte na adresu `https://<address>`.

    Tady `address` je plně kvalifikovaný název domény nebo veřejná IP adresa přiřazená k virtuálnímu počítači Fortigate.

2. Přihlaste se pomocí přihlašovacích údajů správce, které jste zadali během nasazení virtuálního počítače FortiGate.
3. V nabídce na levé straně klikněte na **síť VPN** .
4. V části VPN klikněte na možnost **SSL – portály VPN** .
5. Klikněte na **vytvořit nový** .
6. Zadejte název (obvykle se shoduje se skupinou Azure, která se používá k poskytování vlastního prostředí portálu).
7. Klikněte na znaménko plus ( **+** ) vedle zdrojové fondy IP adres, vyberte výchozí fond a klikněte na **Zavřít** .
8. Přizpůsobte si prostředí pro tuto skupinu. Pro účely testování jde o přizpůsobení zprávy portálu a motivu. Můžete tak také vytvořit vlastní záložky, které uživatele přesměrují na interní prostředky.
9. Klikněte na tlačítko **OK**.
10. Opakujte kroky 5 až 9 pro každou skupinu Azure, která bude mít vlastní prostředí portálu.
11. V části VPN klikněte na **SSL-VPN nastavení** .
12. Klikněte na znaménko plus ( **+** ) vedle pole naslouchat rozhraním.
13. Vyberte **PORT1** a klikněte na **Zavřít** .


14. Pokud byl vlastní certifikát SSL dřív nainstalovaný, změňte certifikát serveru tak, aby používal vlastní certifikát SSL v rozevírací nabídce.
15. V části ověřování/mapování portálu klikněte na **vytvořit novou** .
16. Vyberte první skupinu Azure a porovnejte ji s portálem se stejným názvem.
17. Klikněte na tlačítko **OK**.
18. Opakujte kroky 15 až 17 pro každou dvojici skupiny Azure nebo portálu.
19. V části ověřování/mapování portálu upravte **všechny ostatní uživatele nebo skupiny** .
20. Nastavení portálu na **úplný přístup**
21. Klikněte na tlačítko **OK**.
22. Klikněte na **použít** .
23. Posuňte se na začátek stránky nastavení SSL-VPN a klikněte na upozornění neexistují **zásady SSL-VPN** 
     **. Pokud chcete vytvořit nové zásady SSL-VPN pomocí těchto nastavení, klikněte sem** .
24. Zadejte název, třeba **VPN GRP** .
25. Nastavit odchozí rozhraní na **port**
26. Klikněte na **zdroj** .
27. V části Adresa vyberte **vše** .
28. V části uživatel vyberte první skupinu Azure.
29. Klikněte na **Zavřít** .
30. Klikněte na **cíl** .
31. V části Adresa se obvykle jedná o interní síť. Vyberte login.microsoft.com pro testování
32. Klikněte na **Zavřít** .
33. Klikněte na **Služba** .
34. Kliknout na **vše**
35. Klikněte na **Zavřít** .
36. Klikněte na tlačítko **OK**.
37. V nabídce na levé straně klikněte na **zásady & objekty** .
38. V části zásady & objekty klikněte na **zásady brány firewall** .
39. Rozbalte **protokol SSL-VPN Tunneling Interface (SSL. root) – port >**
40. Klikněte pravým tlačítkem na zásadu sítě VPN vytvořenou dříve ( **VPN GRP 1** ) a vyberte **Kopírovat** .
41. Klikněte pravým tlačítkem na zásady sítě VPN a vyberte **vložit** > **níže** .
42. Upravte nové zásady a poskytněte jí jiný název (řekněme, že **VPN Grp2** ) a Změna skupiny se vztahuje na (jinou skupinu Azure).
43. Klikněte pravým tlačítkem na novou zásadu a nastavte stav **povoleno** .


## <a name="test-sign-in-using-azure"></a>Testování přihlášení pomocí Azure

1. Pomocí relace v privátním prohlížeči přejděte na `https://<address>` 
2. Přihlášení by se mělo přesměrovat na Azure Active Directory pro přihlášení.
3. Po zadání přihlašovacích údajů pro uživatele, který má v tenantovi Azure přiřazenou aplikaci FortiGate, by se měl zobrazit příslušný uživatelský portál.

    ![Fortigate SSL VPN](test-sign-in.png)
