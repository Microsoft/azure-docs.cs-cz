---
title: Řešení Azure VMware od CloudSimple – použití Azure AD jako zdroje identity v privátním cloudu
description: Popisuje, jak přidat Azure AD jako poskytovatele identity ve vašem CloudSimple Privátní cloud k ověření uživatelů přístupu CloudSimple z Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 674ca8bea110d60557d1e50e7b68c9c3f7a92bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564580"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Použití Azure AD jako poskytovatele identity pro vCenter na CloudSimple Privátní cloud

VCenter CloudSimple Private Cloud můžete nastavit tak, aby se ověřoval pomocí služby Azure Active Directory (Azure AD), aby k virtuálnímu počítači měli přístup vaši správci vMware. Po nastavení zdroje identity jednotného přihlášení může uživatel **cloudu** přidat uživatele ze zdroje identity do aplikace vCenter.  

Doménu a řadiče domény služby Active Directory můžete nastavit libovolným z následujících způsobů:

* Místní řadiče domény a domény služby Active Directory
* Domény a řadiče domény služby Active Directory spuštěné v Azure jako virtuální počítače ve vašem předplatném Azure
* Nové domény a řadiče domény služby Active Directory spuštěné ve vašem privátním cloudu CloudSimple
* Služba Azure Active Directory

Tato příručka vysvětluje úkoly potřebné k nastavení Azure AD jako zdroje identity.  Informace o použití místní služby Active Directory nebo služby Active Directory spuštěné v Azure najdete v části [Nastavení zdrojů identit vCenter pro použití služby Active Directory](set-vcenter-identity.md) pro podrobné pokyny při nastavování zdroje identity.

## <a name="about-azure-ad"></a>Informace o Azure AD

Azure AD je služba Microsoft multitenant, cloud založený na adresáři a správy identit.  Azure AD poskytuje škálovatelný, konzistentní a spolehlivý mechanismus ověřování pro uživatele k ověření a přístupu k různým službám v Azure.  Poskytuje také zabezpečené služby LDAP pro všechny služby třetích stran k použití služby Azure AD jako zdroj ověřování nebo identity.  Azure AD kombinuje základní adresářové služby, rozšířené řízení identit a správu přístupu k aplikacím, které lze použít pro poskytování přístupu k privátnímu cloudu pro uživatele, kteří spravují privátní cloud.

Pokud chcete používat Azure AD jako zdroj identity s vCenter, musíte nastavit služby domény Azure AD a Azure AD. Postupujte podle těchto pokynů:

1. [Jak nastavit služby domény Azure AD a Azure AD](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Jak nastavit zdroj identity v privátním cloudu vCenter](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Nastavení doménových služeb Azure AD a Azure AD

Než začnete, budete potřebovat přístup k předplatnému Azure s oprávněními globálního správce.  Následující kroky poskytují obecné pokyny. Podrobnosti jsou obsaženy v dokumentaci k Azure.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Pokud už máte Azure AD, můžete tuto část přeskočit.

1. Nastavte Azure AD na předplatné, jak je popsáno v [dokumentaci K Azure AD](../active-directory/fundamentals/get-started-azure-ad.md).
2. Povolte azure active directory premium na předplatné, jak je popsáno v [Zaregistrujte se pro Azure Active Directory Premium](../active-directory/fundamentals/active-directory-get-started-premium.md).
3. Nastavte vlastní název domény a ověřte vlastní název domény, jak je popsáno v [části Přidání vlastního názvu domény do služby Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md).
    1. Nastavte záznam DNS na registrátora domény s informacemi poskytnutými v Azure.
    2. Nastavte vlastní název domény jako primární doménu.

Volitelně můžete nakonfigurovat další funkce Azure AD.  Ty nejsou nutné pro povolení ověřování vCenter s Azure AD.

### <a name="azure-ad-domain-services"></a>Služby domény Azure AD

> [!NOTE]
> Toto je důležitý krok pro povolení Azure AD jako zdroj identity pro vCenter.  Chcete-li se vyhnout problémům, ujistěte se, že jsou všechny kroky provedeny správně.

1. Povolte doménové služby Azure AD, jak je popsáno v [povolit služby domény Azure Active Directory pomocí webu Azure Portal](../active-directory-domain-services/active-directory-ds-getting-started.md).
2. Nastavte síť, kterou budou používat doménové služby Azure AD, jak je popsáno v [povolit služby Azure Active Directory Domain Services pomocí webu Azure Portal](../active-directory-domain-services/active-directory-ds-getting-started-network.md).
3. Nakonfigurujte skupinu správců pro správu služby Azure AD Domain Services, jak je popsáno v [povolit služby Azure Active Directory Domain Services pomocí portálu Azure](../active-directory-domain-services/active-directory-ds-getting-started-admingroup.md).
4. Aktualizujte nastavení DNS pro službu Azure AD Domain Services, jak je popsáno v [povolit službu Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-dns.md).  Pokud se chcete připojit ke službě AD přes Internet, nastavte záznam DNS pro veřejnou IP adresu doménových služeb Azure AD na název domény.
5. Povolte synchronizaci hodnot hash hesel pro uživatele.  Tento krok umožňuje synchronizaci hodnot hash hesel požadovaných pro NT LAN Manager (NTLM) a ověřování protokolu Kerberos se službou Azure AD Domain Services. Po nastavení synchronizace hodnot hash hesel se uživatelé můžou přihlásit ke spravované doméně s použitím podnikových přihlašovacích údajů. Viz [Povolení synchronizace hash hesel se službou Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).
    1. Pokud jsou přítomni pouze cloudoví uživatelé, musí změnit své heslo pomocí <a href="http://myapps.microsoft.com/" target="_blank">přístupového panelu Azure AD,</a> aby zajistili, že se ukládají stavy hashe hesel ve formátu vyžadovaném protokolem NTLM nebo Kerberos.  Postupujte podle pokynů v části [Povolit synchronizaci hash hesel do spravované domény pro uživatelské účty pouze v cloudu](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).  Tento krok je nutné provést pro jednotlivé uživatele a všechny nové uživatele, který je vytvořen ve vašem adresáři Azure AD pomocí webu Azure Portal nebo Rutiny Azure AD PowerShell. Uživatelé, kteří vyžadují přístup ke službám domény Azure AD, musí používat <a href="http://myapps.microsoft.com/" target="_blank">přístupový panel Azure AD</a> a ke svému profilu přistupovat ke změně hesla.

        > [!NOTE]
        > Pokud má vaše organizace výhradně uživatelské účty jenom cloudu, všichni uživatelé, kteří potřebují používat službu Azure Active Directory Domain Services, si musí změnit heslo. Uživatelský účet jenom cloudu je účet vytvořený v adresáři služby Azure AD pomocí webu Azure Portal nebo rutin Azure AD PowerShellu. Takové uživatelské účty se nesynchronizují z místního adresáře.

    2. Pokud synchronizujete hesla z místního adresáře Active Directory, postupujte podle pokynů v [dokumentaci služby Active Directory](../active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md).

6.  Nakonfigurujte zabezpečený protokol LDAP ve službě Azure Active Directory Domain Services, jak je popsáno v [seznamu Konfigurace zabezpečeného protokolu LDAP (LDAPS) pro spravovanou doménu služby Azure AD Domain Services](../active-directory-domain-services/tutorial-configure-ldaps.md).
    1. Nahrajte certifikát pro použití zabezpečeným protokolem LDAP, jak je popsáno v [tématu Azure, získejte certifikát pro zabezpečené protokol LDAP](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap).  CloudSimple doporučuje používat podepsaný certifikát vydaný certifikační autoritou, aby bylo zajištěno, že vCenter může certifikátu důvěřovat.
    2. Povolte zabezpečené protokol LDAP, jak je popsáno [Povolit zabezpečené ldap (LDAPS) pro spravovanou doménu služby Azure AD Domain Services](../active-directory-domain-services/tutorial-configure-ldaps.md).
    3. Uložte veřejnou část certifikátu (bez soukromého klíče) ve formátu CER pro použití s programem vCenter při konfiguraci zdroje identity.
    4. Pokud je vyžadován přístup k internetu ke službám domény Azure AD, povolte možnost Povolit zabezpečený přístup k protokolu LDAP přes internet.
    5. Přidejte pravidlo příchozího zabezpečení pro skupinu NSG služby Domény Azure AD pro port TCP 636.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Nastavení zdroje identity v privátním cloudu vCenter

1. [Eskalovat oprávnění](escalate-private-cloud-privileges.md) pro váš private cloud vCenter.
2. Shromážděte parametry konfigurace potřebné pro nastavení zdroje identity.

    | **Možnost** | **Popis** |
    |------------|-----------------|
    | **Název** | Název zdroje identity. |
    | **Základní DN pro uživatele** | Základní rozlišující název pro uživatele.  Pro Azure AD `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` použijte: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`Příklad: .|
    | **Název domény** | Například example.com. Do tohoto textového pole nezadejte adresu IP. |
    | **Alias domény** | *(nepovinné)* Název netbios domény. Pokud používáte ověřování SSPI, přidejte název netbios domény služby Active Directory jako alias zdroje identity. |
    | **Základní DN pro skupiny** | Základní rozlišující název pro skupiny. Pro Azure AD `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` použijte: Příklad:`OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **Adresa URL primárního serveru** | Primární server LDAP řadiče domény pro doménu.<br><br>Použijte formát `ldaps://hostname:port`. Port je obvykle 636 pro připojení LDAPS. <br><br>Při použití `ldaps://` v primární nebo sekundární adrese URL LDAP je vyžadován certifikát, který vytváří vztah důvěryhodnosti pro koncový bod LDAPS serveru Active Directory. |
    | **Adresa URL sekundárního serveru** | Adresa sekundárního serveru LDAP řadiče domény, který se používá pro převzetí služeb při selhání. |
    | **Zvolte certifikát** | Chcete-li používat protokol LDAPS se serverem LDAP služby Active Directory nebo zdrojem `ldaps://` identity serveru OpenLDAP Server, zobrazí se po zadání textového pole URL tlačítko Zvolit certifikát. Sekundární adresa URL není vyžadována. |
    | **Username** | ID uživatele v doméně, který má minimální přístup jen pro čtení k základní dn pro uživatele a skupiny. |
    | **Heslo** | Heslo uživatele, které je určeno uživatelským jménem. |

3. Po eskalaci oprávnění se přihlaste k privátnímu cloudu vCenter.
4. Postupujte podle pokynů v [části Přidání zdroje identity do centra vCenter](set-vcenter-identity.md#add-an-identity-source-on-vcenter) pomocí hodnot z předchozího kroku k nastavení služby Azure Active Directory jako zdroje identity.
5. Přidejte uživatele nebo skupiny z Azure AD do skupin vCenter, jak je popsáno v tématu VMware [Přidat členy do skupiny pro jednotné přihlášení vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

> [!CAUTION]
> Noví uživatelé musí být přidáni pouze do *skupiny Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* *nebo, Cloud-Global-VM-Admin-Group*.  Uživatelé připřidání do *skupiny Administrators* budou automaticky odebráni.  Do *skupiny Administrators* musí být přidány pouze účty služeb.

## <a name="next-steps"></a>Další kroky

* [Informace o modelu oprávnění privátního cloudu](learn-private-cloud-permissions.md)
