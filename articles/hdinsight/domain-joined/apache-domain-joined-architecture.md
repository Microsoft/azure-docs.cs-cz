---
title: Architektura Azure HDInsight s Balíček zabezpečení podniku
description: Naučte se plánovat zabezpečení Azure HDInsight pomocí Balíček zabezpečení podniku.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/11/2020
ms.openlocfilehash: 452a3b04637126b40aca907178bebd6f74ec4481
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "79365765"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Použití Balíčku zabezpečení podniku ve službě HDInsight

Standardní cluster Azure HDInsight je cluster s jedním uživatelem. Je vhodný pro většinu společností, které mají menší aplikační týmy vytvářející velké datové úlohy. Každý uživatel může vytvořit vyhrazený cluster na vyžádání a zničit ho, když už ho nepotřebujete.

Řada podniků se přesunula k modelu, ve kterém IT týmy spravují clustery, a několik týmů aplikací sdílí clustery. Tyto větší podniky potřebují pro každý cluster ve službě Azure HDInsight přístup s více uživateli.

HDInsight spoléhá na oblíbeného poskytovatele identity – Active Directory – spravovaný způsob. Integrací služby HDInsight s [Azure Active Directory Domain Services (Azure služba AD DS)](../../active-directory-domain-services/overview.md)můžete ke clusterům přistupovat pomocí svých přihlašovacích údajů do domény.

Virtuální počítače v HDInsight jsou připojené k doméně zadané doméně. Všechny služby spuštěné v HDInsight (Apache Ambari, Apache Hive Server, Apache Ranger, Apache Spark Thrift Server a další) pro ověřeného uživatele bezproblémově fungují. Správci pak můžou vytvořit silné zásady autorizace pomocí Apache Ranger a poskytovat řízení přístupu na základě rolí pro prostředky v clusteru.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrace služby HDInsight s Active Directory

Open Source Apache Hadoop spoléhá na protokol Kerberos pro ověřování a zabezpečení. Uzly clusteru HDInsight s Balíček zabezpečení podniku (ESP) jsou proto připojené k doméně spravované pomocí Azure služba AD DS. Zabezpečení protokolu Kerberos je nakonfigurované pro součásti systému Hadoop v clusteru.

Automaticky se vytvoří tyto věci:

- Instanční objekt pro každou komponentu Hadoop
- Objekt zabezpečení počítače pro každý počítač, který je připojený k doméně
- Organizační jednotka (OU) pro každý cluster, do které se mají ukládat tyto služby a objekty zabezpečení počítače

Pro Shrnutí musíte nastavit prostředí pomocí:

- Doména služby Active Directory (spravovaná pomocí Azure služba AD DS). **Aby bylo možné pracovat se službou Azure HDInsight, název domény musí mít 39 znaků nebo méně.**
- Protokol Secure LDAP (LDAPs) povoleno ve službě Azure služba AD DS.
- Správné síťové připojení z virtuální sítě HDInsight do služby Azure služba AD DS Virtual Network, pokud pro ně zvolíte samostatné virtuální sítě. Virtuální počítač uvnitř virtuální sítě HDInsight by měl mít přehled o službě Azure služba AD DS prostřednictvím partnerského vztahu virtuálních sítí. Pokud jsou služby HDInsight a Azure služba AD DS nasazeny ve stejné virtuální síti, je připojení automaticky zajištěno a není nutné provádět žádné další akce.

## <a name="set-up-different-domain-controllers"></a>Nastavení různých řadičů domény

HDInsight aktuálně podporuje jenom Azure služba AD DS jako hlavní řadič domény, který cluster používá pro komunikaci pomocí protokolu Kerberos. Ale další komplexní nastavení služby Active Directory je možné, pokud nastavení vede k povolení služby Azure služba AD DS pro přístup k HDInsight.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure služba AD DS](../../active-directory-domain-services/overview.md) poskytuje spravovanou doménu, která je plně kompatibilní se službou Windows Server Active Directory. Microsoft se postará o správu, opravy a monitorování domény v nastavení vysoké dostupnosti (HA). Cluster můžete nasadit, aniž byste se museli starat o údržbu řadičů domény.

Uživatelé, skupiny a hesla se synchronizují ze služby Azure AD. Jednosměrná synchronizace z vaší instance Azure AD do Azure služba AD DS umožňuje uživatelům přihlásit se ke clusteru pomocí stejných podnikových přihlašovacích údajů.

Další informace najdete v tématu [konfigurace clusterů HDInsight s protokolem ESP pomocí Azure služba AD DS](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Místní služba Active Directory nebo Active Directory na virtuálních počítačích s IaaS

Pokud máte místní instanci služby Active Directory nebo složitější nastavení služby Active Directory pro vaši doménu, můžete tyto identity synchronizovat do Azure AD pomocí Azure AD Connect. Pak můžete povolit Azure služba AD DS v tomto tenantovi Active Directory.

Vzhledem k tomu, že protokol Kerberos spoléhá na hodnoty hash hesla, musíte [Povolit synchronizaci hodnot hash hesel v Azure služba AD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).

Pokud používáte federaci s Active Directory Federation Services (AD FS) (AD FS), musíte povolit synchronizaci hodnot hash hesel. (Pro doporučené nastavení se podívejte na [Toto video](https://youtu.be/qQruArbu2Ew).) Synchronizace hodnot hash hesel pomáhá při zotavení po havárii v případě selhání infrastruktury AD FS a také pomáhá zajistit ochranu proti nevráceným přihlašovacím údajům. Další informace najdete v tématu [povolení synchronizace hodnot hash hesel pomocí Azure AD Connect synchronizace](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

Použití místní služby Active Directory nebo Active Directory na virtuálních počítačích s IaaS bez použití Azure AD a Azure služba AD DS není podporovanou konfigurací pro clustery HDInsight s protokolem ESP.

Pokud se používá federační služba a hodnoty hash hesel se synchronizují správně, ale dojde k chybám ověřování, ověřte, jestli je pro instanční objekt PowerShellu povolené ověřování heslem cloudu. Pokud ne, musíte nastavit [zásadu zjišťování domovské sféry (HRD)](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) pro vašeho TENANTA Azure AD. Postup kontroly a nastavení zásad HRD:

1. Nainstalujte si náhled [modulu Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

   ```powershell
   Install-Module AzureAD
   ```

2. Připojte se pomocí přihlašovacích údajů globálního správce (Správce klienta).

   ```powershell
   Connect-AzureAD
   ```

3. Ověřte, zda již byl vytvořen Microsoft Azure PowerShell instanční objekt.

   ```powershell
   Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. Pokud neexistuje, vytvořte instanční objekt.

   ```powershell
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. Vytvořte a připojte zásadu k tomuto instančnímu objektu.

   ```powershell
    # Determine whether policy exists
    Get-AzureADPolicy | Where {$_.DisplayName -eq "EnableDirectAuth"}

    # Create if not exists
    $policy = New-AzureADPolicy `
        -Definition @('{"HomeRealmDiscoveryPolicy":{"AllowCloudPasswordValidation":true}}') `
        -DisplayName "EnableDirectAuth" `
        -Type "HomeRealmDiscoveryPolicy"

    # Determine whether a policy for the service principal exist
    Get-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId

    # Add a service principal policy if not exist
    Add-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId `
        -refObjectID $policy.ID
   ```

## <a name="next-steps"></a>Další kroky

- [Konfigurace clusterů HDInsight s ESP](apache-domain-joined-configure-using-azure-adds.md)
- [Konfigurace zásad Apache Hive pro clustery HDInsight s ESP](apache-domain-joined-run-hive.md)
- [Správa clusterů HDInsight pomocí protokolu ESP](apache-domain-joined-manage.md)
