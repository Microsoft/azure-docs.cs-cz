---
title: Řešení Azure VMware podle CloudSimple – použití Azure AD jako zdroje identity v privátním cloudu
description: Popisuje, jak přidat Azure AD jako zprostředkovatele identity do privátního cloudu CloudSimple k ověřování uživatelů, kteří přistupují k CloudSimple z Azure.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f55a0f52f5e028f9cbf7a9fabbb3c24ad43c3800
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898602"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Použití Azure AD jako zprostředkovatele identity pro vCenter v privátním cloudu CloudSimple

Můžete nastavit CloudSimple privátní cloud vCenter pro ověřování pomocí Azure Active Directory (Azure AD) pro správce VMware pro přístup k serveru vCenter. Po nastavení zdroje identity jednotného přihlašování může uživatel **cloudowner** přidat uživatele ze zdroje identity do vCenter.  

Doménu a řadiče domény služby Active Directory můžete nastavit některým z těchto způsobů:

* Doména a řadiče domény služby Active Directory místně spuštěné
* Doména a řadiče domény služby Active Directory běžící na Azure jako virtuální počítače ve vašem předplatném Azure
* Nová doména a řadiče domény služby Active Directory běžící v privátním cloudu CloudSimple
* Služba Azure Active Directory

Tato příručka vysvětluje úkoly potřebné k nastavení služby Azure AD jako zdroje identity.  Informace o používání místní služby Active Directory nebo Active Directory běžící v Azure najdete v tématu nastavení [zdrojů identit vCenter pro použití služby Active Directory](set-vcenter-identity.md) pro podrobné pokyny k nastavení zdroje identity.

## <a name="about-azure-ad"></a>Informace o Azure AD

Azure AD je cloudová služba Microsoftu pro více tenantů a službu pro správu identit.  Azure AD poskytuje škálovatelný, konzistentní a spolehlivý ověřovací mechanismus pro uživatele, kteří budou ověřovat a přistupovat k různým službám v Azure.  Poskytuje taky zabezpečené služby LDAP pro všechny služby třetích stran, aby používaly Azure AD jako zdroj ověřování/identity.  Azure AD kombinuje základní adresářové služby, pokročilé řízení identit a správu přístupu k aplikacím, které se dají použít k poskytnutí přístupu k privátnímu cloudu pro uživatele, kteří spravují privátní cloud.

Pokud chcete používat Azure AD jako zdroj identity s vCenter, musíte nastavit Azure AD a službu Azure AD Domain Services. Postupujte podle těchto pokynů:

1. [Jak nastavit Azure AD a Azure AD Domain Services](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Jak nastavit zdroj identity v rámci vašeho privátního cloudu vCenter](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Nastavení služeb Azure AD a Azure AD Domain Services

Než začnete, budete potřebovat přístup k vašemu předplatnému Azure s oprávněními globálního správce.  Následující kroky poskytují obecné pokyny. Podrobnosti jsou obsaženy v dokumentaci k Azure.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Pokud už službu Azure AD máte, můžete tuto část přeskočit.

1. Nastavte Azure AD v předplatném, jak je popsáno v  [dokumentaci k Azure AD](../active-directory/fundamentals/active-directory-whatis.md).
2. Povolte Azure Active Directory Premium v předplatném, jak je popsáno v tématu [Registrace pro Azure Active Directory Premium](../active-directory/fundamentals/active-directory-get-started-premium.md).
3. Nastavte vlastní název domény a ověřte název vlastní domény, jak je popsáno v tématu [Přidání vlastního názvu domény do Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md).
    1. Nastavte v doménovém registrátoru záznam DNS s informacemi, které jsou k dispozici v Azure.
    2. Nastavte název vlastní domény na primární doménu.

Volitelně můžete nakonfigurovat další funkce služby Azure AD.  Nevyžadují se pro povolení ověřování vCenter pomocí Azure AD.

### <a name="azure-ad-domain-services"></a>Služba Azure AD Domain Services

> [!NOTE]
> Toto je důležitý krok pro povolení služby Azure AD jako zdroje identity pro vCenter.  Abyste se vyhnuli jakýmkoli problémům, ujistěte se, že jsou všechny kroky provedeny správně.

1. Povolte službu Azure AD Domain Services, jak je popsáno v tématu [povolení Azure Active Directory Domain Services pomocí Azure Portal](../active-directory-domain-services/tutorial-create-instance.md).
2. Nastavte síť, která bude používána službou Azure AD Domain Services, jak je popsáno v tématu [povolení Azure Active Directory Domain Services pomocí Azure Portal](../active-directory-domain-services/tutorial-create-instance.md).
3. Nakonfigurujte skupinu správců pro správu Azure AD Domain Services, jak je popsáno v tématu [povolení Azure Active Directory Domain Services pomocí Azure Portal](../active-directory-domain-services/tutorial-create-instance.md).
4. Aktualizujte nastavení DNS pro váš Azure AD Domain Services, jak je popsáno v tématu [povolení Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md).  Pokud se chcete připojit ke službě AD přes Internet, nastavte záznam DNS pro veřejnou IP adresu služby Azure AD Domain Services na název domény.
5. Povolit synchronizaci hodnot hash hesel pro uživatele.  Tento krok umožňuje synchronizaci hodnot hash hesel vyžadovaných pro Azure AD Domain Services ověřování NT LAN Manageru (NTLM) a Kerberos. Po nastavení synchronizace hodnot hash hesel se uživatelé můžou přihlásit ke spravované doméně s použitím podnikových přihlašovacích údajů. Další informace najdete v tématu [povolení synchronizace hodnot hash hesel pro Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md).
    1. Pokud jsou k dispozici pouze cloudní uživatelé, musí změnit heslo pomocí <a href="https://myapps.microsoft.com/" target="_blank">přístupového panelu Azure AD</a> , aby bylo zajištěno, že hodnoty hash hesel budou uloženy ve formátu VYŽADOVANÉm protokolem NTLM nebo Kerberos.  Postupujte podle pokynů v tématu [povolení synchronizace hodnot hash hesel do spravované domény pro uživatelské účty výhradně pro Cloud](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).  Tento krok je potřeba provést pro jednotlivé uživatele a každého nového uživatele, který je vytvořený v adresáři Azure AD pomocí rutin Azure Portal nebo Azure AD PowerShellu. Uživatelé, kteří potřebují přístup ke službě Azure AD Domain Services, musí použít <a href="https://myapps.microsoft.com/" target="_blank">přístupový panel Azure AD</a> a získat přístup ke svému profilu, aby změnili heslo.

        > [!NOTE]
        > Pokud má vaše organizace výhradně uživatelské účty jenom cloudu, všichni uživatelé, kteří potřebují používat službu Azure Active Directory Domain Services, si musí změnit heslo. Uživatelský účet jenom cloudu je účet vytvořený v adresáři služby Azure AD pomocí webu Azure Portal nebo rutin Azure AD PowerShellu. Takové uživatelské účty se nesynchronizují z místního adresáře.

    2. Pokud synchronizujete hesla z místní služby Active Directory, postupujte podle kroků v [dokumentaci ke službě Active Directory](../active-directory-domain-services/tutorial-configure-password-hash-sync.md).

6.  Nakonfigurujte zabezpečený protokol LDAP v Azure Active Directory Domain Services, jak je popsáno v tématu [Konfigurace protokolu Secure LDAP (LDAPS) pro spravovanou doménu Azure AD Domain Services](../active-directory-domain-services/tutorial-configure-ldaps.md).
    1. Nahrajte certifikát pro použití zabezpečeným protokolem LDAP, jak je popsáno v tématu Azure [získání certifikátu pro zabezpečený protokol LDAP](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap).  CloudSimple doporučuje používat podepsaný certifikát vydaný certifikační autoritou k zajištění, že vCenter může certifikát důvěřovat.
    2. Povolit zabezpečený protokol LDAP jak je popsáno, [Povolení zabezpečení LDAP (LDAPS) pro Azure AD Domain Services spravovanou doménu](../active-directory-domain-services/tutorial-configure-ldaps.md).
    3. Uložte veřejnou část certifikátu (bez privátního klíče) do formátu. cer pro použití s vCenter při konfiguraci zdroje identity.
    4. Pokud je potřeba internetový přístup ke službě Azure AD Domain Services, povolte možnost Povolit zabezpečený přístup k LDAP přes Internet.
    5. Přidejte příchozí pravidlo zabezpečení pro službu Azure AD Domain Services NSG pro port TCP 636.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Nastavení zdroje identity v rámci vašeho privátního cloudu vCenter

1. [Eskalace oprávnění](escalate-private-cloud-privileges.md) pro váš privátní cloud vCenter.
2. Shromážděte konfigurační parametry požadované pro nastavení zdroje identity.

    | **Možnost** | **Popis** |
    |------------|-----------------|
    | **Název** | Název zdroje identity |
    | **Základní rozlišující název pro uživatele** | Základní rozlišující název pro uživatele  Pro Azure AD použijte: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  Příklad: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com` .|
    | **Název domény** | Plně kvalifikovaný název domény pro doménu, například example.com. V tomto textovém poli nezadávejte IP adresu. |
    | **Alias domény** | *(volitelné)* Název domény pro rozhraní NetBIOS. Pokud používáte ověřování pomocí rozhraní SSPI, přidejte název domény služby Active Directory jako alias zdroje identity. |
    | **Základní rozlišující název pro skupiny** | Základní rozlišující název pro skupiny Pro Azure AD použijte: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  Příklad: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **Adresa URL primárního serveru** | Server LDAP primárního řadiče domény pro doménu.<br><br>Použijte formát `ldaps://hostname:port`. Port je obvykle 636 pro připojení LDAPs. <br><br>Certifikát, který vytváří vztah důvěryhodnosti pro koncový bod LDAPs serveru Active Directory, se vyžaduje při použití `ldaps://` v primární nebo sekundární adrese URL protokolu LDAP. |
    | **Adresa URL sekundárního serveru** | Adresa serveru LDAP sekundárního řadiče domény, který se používá pro převzetí služeb při selhání. |
    | **Zvolit certifikát** | Pokud chcete použít LDAPs se serverem služby Active Directory LDAP nebo zdrojem identity serveru OpenLDAP, zobrazí se po zadání `ldaps://` v textovém poli Adresa URL tlačítko zvolit certifikát. Sekundární adresa URL není povinná. |
    | **Uživatelské jméno** | ID uživatele v doméně, který má minimální přístup jen pro čtení k základnímu rozlišujícímu názvu pro uživatele a skupiny. |
    | **Heslo** | Heslo uživatele, který je určen uživatelským jménem. |

3. Po eskalaci oprávnění se přihlaste k privátnímu cloudu vCenter.
4. Podle pokynů v části [Přidání zdroje identity na vCenter](set-vcenter-identity.md#add-an-identity-source-on-vcenter) použijte hodnoty z předchozího kroku a nastavte Azure Active Directory jako zdroj identity.
5. Přidat uživatele/skupiny z Azure AD do skupin vCenter, jak je popsáno v tématu o VMware [Přidání členů do skupiny vCenter Single Sign-On](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

> [!CAUTION]
> Noví uživatelé musí být přidáni pouze *ke skupině* Cloud-Owner-Group *-Global-cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin* -Group nebo, *Cloud-Global-VM-admin-Group*.  Uživatelé přidaní do skupiny *správců* se automaticky odeberou.  Do skupiny *Administrators* musí být přidáni pouze účty služeb.

## <a name="next-steps"></a>Další kroky

* [Další informace o modelu oprávnění privátního cloudu](learn-private-cloud-permissions.md)
