---
title: Řešení Azure VMware od CloudSimple – nastavení zdrojů identit y vCenter v privátním cloudu
description: Popisuje, jak nastavit vcenter Pricloud pro ověřování pomocí služby Active Directory pro správce vMware pro přístup k programu vCenter.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5355e43ca6ac075e76a76ceb51be135cf4b62b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564019"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>Nastavení zdrojů identit vCenter pro použití služby Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>O zdrojích identit vCenter vMware

VMware vCenter podporuje různé zdroje identit pro ověřování uživatelů, kteří přistupují k programu vCenter.  VCenter CloudSimple Private Cloud lze nastavit tak, aby se ověřovali pomocí služby Active Directory, aby správci společnosti VMware měli přístup k programu vCenter. Po dokončení instalace může uživatel **cloudu** přidat uživatele ze zdroje identity do aplikace vCenter.  

Doménu a řadiče domény služby Active Directory můžete nastavit libovolným z následujících způsobů:

* Místní řadiče domény a domény služby Active Directory
* Domény a řadiče domény služby Active Directory spuštěné v Azure jako virtuální počítače ve vašem předplatném Azure
* Nová doména a řadiče domény služby Active Directory spuštěné v privátním cloudu
* Služba Azure Active Directory

Tato příručka vysvětluje úkoly pro nastavení domény služby Active Directory a řadičů domény spuštěných v místním prostředí nebo jako virtuálnípočítače ve vašich předplatných.  Pokud chcete použít Azure AD jako zdroj identity, najdete v části [Použití Azure AD jako zprostředkovatele identity pro vCenter na CloudSimple Private Cloud](azure-ad.md) pro podrobné pokyny při nastavování zdroje identity.

Před [přidáním zdroje identity](#add-an-identity-source-on-vcenter)dočasně [eskalujte oprávnění programu vCenter](escalate-private-cloud-privileges.md).

> [!CAUTION]
> Noví uživatelé musí být přidáni pouze do *skupiny Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* *nebo, Cloud-Global-VM-Admin-Group*.  Uživatelé připřidání do *skupiny Administrators* budou automaticky odebráni.  K přihlášení k webovému uživatelskému rozhraní vSphere je nutné přidat pouze účty služeb a účty služeb *administrators* a účty služeb.   


## <a name="identity-source-options"></a>Možnosti zdroje identity

* [Přidání místní služby Active Directory jako zdroje identity jednotného přihlášení](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Nastavení nové služby Active Directory v privátním cloudu](#set-up-new-active-directory-on-a-private-cloud)
* [Nastavení služby Active Directory v Azure](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>Přidání místního služby Active Directory jako zdroje identity jednotného přihlášení

Chcete-li nastavit místní službu Active Directory jako zdroj identity jednotného přihlášení, potřebujete:

* [Připojení VPN site-to-Site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) z místního datového centra do privátního cloudu.
* Místní IP adresa serveru DNS byla přidána do řadiče vCenter a platformových služeb (PSC).

Při nastavování domény služby Active Directory použijte informace v následující tabulce.

| **Možnost** | **Popis** |
|------------|-----------------|
| **Název** | Název zdroje identity. |
| **Základní DN pro uživatele** | Základní rozlišující název pro uživatele. |
| **Název domény** | Například example.com. Do tohoto textového pole nezadejte adresu IP. |
| **Alias domény** | Název netbios domény. Pokud používáte ověřování SSPI, přidejte název netbios domény služby Active Directory jako alias zdroje identity. |
| **Základní DN pro skupiny** | Základní rozlišující název pro skupiny. |
| **Adresa URL primárního serveru** | Primární server LDAP řadiče domény pro doménu.<br><br>Použijte formát `ldap://hostname:port`  `ldaps://hostname:port`nebo . Port je obvykle 389 pro připojení LDAP a 636 pro připojení LDAPS. Pro nasazení řadičů s více doménami služby Active Directory je port obvykle 3268 pro ldap a 3269 pro LDAPS.<br><br>Při použití `ldaps://` v primární nebo sekundární adrese URL LDAP je vyžadován certifikát, který vytváří vztah důvěryhodnosti pro koncový bod LDAPS serveru Active Directory. |
| **Adresa URL sekundárního serveru** | Adresa sekundárního serveru LDAP řadiče domény, který se používá pro převzetí služeb při selhání. |
| **Zvolte certifikát** | Chcete-li používat protokol LDAPS se serverem LDAP služby Active Directory nebo zdrojem `ldaps://` identity serveru OpenLDAP Server, zobrazí se po zadání textového pole URL tlačítko Zvolit certifikát. Sekundární adresa URL není vyžadována. |
| **Username** | ID uživatele v doméně, který má minimální přístup jen pro čtení k základní dn pro uživatele a skupiny. |
| **Heslo** | Heslo uživatele, které je určeno uživatelským jménem. |

Pokud máte informace v předchozí tabulce, můžete přidat místní službu Active Directory jako zdroj identity jednotného přihlášení do aplikace vCenter.

> [!TIP]
> Další informace o zdrojích identity jednotného přihlašování najdete na [stránce dokumentace společnosti VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html).

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>Nastavení nové služby Active Directory v privátním cloudu

V privátním cloudu můžete nastavit novou doménu služby Active Directory a použít ji jako zdroj identity pro jednotné přihlašování.  Doména služby Active Directory může být součástí existující doménové struktury služby Active Directory nebo může být nastavena jako nezávislá doménová struktura.

### <a name="new-active-directory-forest-and-domain"></a>Nová doménová struktura a doména služby Active Directory

Chcete-li nastavit novou doménovou strukturu a doménu služby Active Directory, potřebujete:

* Jeden nebo více virtuálních počítačů se systémem Microsoft Windows Server, které lze použít jako řadiče domény pro novou doménovou strukturu a doménu služby Active Directory.
* Jeden nebo více virtuálních počítačů se službou DNS pro překlad názvů.

Podrobné kroky naleznete [v tématu Instalace nové doménové struktury služby Active Directory systému Windows Server 2012.](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-)

> [!TIP]
> Pro vysokou dostupnost služeb doporučujeme nastavit více řadičů domény a serverů DNS.

Po nastavení doménové struktury a domény služby Active Directory můžete [přidat zdroj identity do programu vCenter](#add-an-identity-source-on-vcenter) pro novou službu Active Directory.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Nová doména služby Active Directory v existující doménové struktuře služby Active Directory

Chcete-li nastavit novou doménu služby Active Directory v existující doménové struktuře služby Active Directory, potřebujete:

* Připojení VPN mezi lokalitami k umístění doménové struktury služby Active Directory.
* Server DNS, chcete-li přeložit název existující doménové struktury služby Active Directory.

Podrobné kroky naleznete [v tématu Instalace nové podřízené nebo stromové domény služby Active Directory ve windowsserveru 2012.](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-)

Po nastavení domény služby Active Directory můžete [přidat zdroj identity do programu vCenter](#add-an-identity-source-on-vcenter) pro novou službu Active Directory.

## <a name="set-up-active-directory-on-azure"></a>Nastavení služby Active Directory v Azure

Služba Active Directory spuštěná v Azure je podobná službě Active Directory, která běží místně.  Chcete-li nastavit službu Active Directory spuštěnou v Azure jako zdroj identity jednotného přihlášení v centru vCenter, musí mít server vCenter a PSC síťové připojení k virtuální síti Azure, kde jsou spuštěny služby Active Directory.  Toto připojení můžete vytvořit pomocí [připojení virtuální sítě Azure pomocí ExpressRoute](azure-expressroute-connection.md) z virtuální sítě Azure, kde služby Active Directory běží do cloudového privátního cloudu.

Po navázání síťového připojení postupujte podle pokynů v [části Přidání místní ho služby Active Directory jako zdroje identity jednotného přihlášení](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) a přidejte jej jako zdroj identity.  

## <a name="add-an-identity-source-on-vcenter"></a>Přidání zdroje identity do aplikace vCenter

1. [Eskalovat oprávnění](escalate-private-cloud-privileges.md) ve vašem privátním cloudu.

2. Přihlaste se do virtuálního centra pro svůj privátní cloud.

3. Vyberte **možnost Správa domácí>**.

    ![Správa](media/OnPremAD01.png)

4. Vyberte **možnost Jednotné přihlášení > konfigurace**.

    ![Jednotné přihlašování](media/OnPremAD02.png)

5. Otevřete kartu **Zdroje identity** a kliknutím **+** přidáte nový zdroj identity.

    ![Zdroje identity](media/OnPremAD03.png)

6. Vyberte **službu Active Directory jako server LDAP** a klepněte na tlačítko **Další**.

    ![Active Directory](media/OnPremAD04.png)

7. Zadejte parametry zdroje identity pro vaše prostředí a klepněte na tlačítko **Další**.

    ![Active Directory](media/OnPremAD05.png)

8. Zkontrolujte nastavení a klepněte na tlačítko **Dokončit**.
