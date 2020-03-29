---
title: Architektura Azure HDInsight s balíčkem podnikového zabezpečení
description: Přečtěte si, jak naplánovat zabezpečení Azure HDInsight pomocí balíčku enterprise security.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/11/2020
ms.openlocfilehash: 452a3b04637126b40aca907178bebd6f74ec4481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365765"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Použití Balíčku zabezpečení podniku ve službě HDInsight

Standardní cluster Azure HDInsight je cluster pro jednoho uživatele. Je vhodný pro většinu společností, které mají menší aplikační týmy, které budují velké datové úlohy. Každý uživatel může vytvořit vyhrazený cluster na vyžádání a zničit ho, když už není potřeba.

Mnoho podniků se přesunulo k modelu, ve kterém týmy IT spravují clustery a více aplikačních týmů sdílí clustery. Tyto větší podniky potřebují víceuživatelský přístup ke každému clusteru v Azure HDInsight.

HDInsight se spravovaným způsobem spoléhá na oblíbeného poskytovatele identity – službu Active Directory. Integrací HDInsightu se [službou Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/overview.md)můžete ke clusterům přistupovat pomocí přihlašovacích údajů k doméně.

Virtuální počítače (VM) v HDInsight jsou doména připojená k vaší doméně. Takže všechny služby spuštěné na HDInsight (Apache Ambari, Server Apache Hive, Apache Ranger, Setrvační server Apache Spark a další) fungují bez problémů pro ověřeného uživatele. Správci pak mohou vytvářet silné zásady autorizace pomocí Apache Ranger k poskytování řízení přístupu na základě rolí pro prostředky v clusteru.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrace služby HDInsight s Active Directory

Open-source Apache Hadoop spoléhá na protokol Kerberos pro ověřování a zabezpečení. Proto uzly clusteru HDInsight s balíčkem enterprise security (ESP) jsou připojeny k doméně, která je spravovaná Službou Azure AD DS. Zabezpečení protokolu Kerberos je nakonfigurováno pro součásti Hadoop v clusteru.

Následující věci jsou vytvořeny automaticky:

- Instanční objekt pro každou součást Hadoop
- Hlavní objekt počítače pro každý počítač, který je připojen k doméně
- Organizační jednotka (OU) pro každý cluster pro uložení těchto objektů služby a objektů zabezpečení počítače

Chcete-li to shrnout, je třeba nastavit prostředí pomocí:

- Doména služby Active Directory (spravovaná službou Azure AD DS). **Aby bylo možné pracovat s Azure HDInsight, musí být název domény o 39 znacích nebo méně.**
- Zabezpečené LDAP (LDAPS) povolené v Azure AD DS.
- Správné síťové připojení z virtuální sítě HDInsight k virtuální síti Azure AD DS, pokud pro ně zvolíte samostatné virtuální sítě. Virtuální počítač uvnitř virtuální sítě HDInsight by měl mít zorné pole na Azure AD DS prostřednictvím partnerského vztahu virtuální sítě. Pokud hdinsight a Azure AD DS jsou nasazeny ve stejné virtuální síti, připojení se automaticky poskytuje a není potřeba žádná další akce.

## <a name="set-up-different-domain-controllers"></a>Nastavení různých řadičů domény

HDInsight v současné době podporuje pouze Azure AD DS jako hlavní řadič domény, který cluster používá pro komunikaci Kerberos. Ale další složité nastavení služby Active Directory jsou možné, pokud takové nastavení vede k povolení Azure AD DS pro HDInsight přístup.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure AD DS](../../active-directory-domain-services/overview.md) poskytuje spravovanou doménu, která je plně kompatibilní se službou Windows Server Active Directory. Společnost Microsoft se stará o správu, opravy a monitorování domény ve vysoce dostupném nastavení (HA). Cluster můžete nasadit bez obav o údržbu řadičů domény.

Uživatelé, skupiny a hesla jsou synchronizovány z Azure AD. Jednosměrná synchronizace z instance Azure AD do služby Azure AD DS umožňuje uživatelům přihlásit se ke clusteru pomocí stejných podnikových přihlašovacích údajů.

Další informace [najdete v tématu Konfigurace clusterů HDInsight s ESP pomocí Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Místní služba Active Directory nebo služba Active Directory na virtuálních počítačích IaaS

Pokud máte místní instanci služby Active Directory nebo složitější nastavení služby Active Directory pro vaši doménu, můžete tyto identity synchronizovat s Azure AD pomocí služby Azure AD Connect. Potom můžete povolit Azure AD DS v tomto klientovi služby Active Directory.

Vzhledem k tomu, že protokol Kerberos spoléhá na hodnotu hash hesel, je nutné [povolit synchronizaci hodnot hash hesel ve službě Azure AD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).

Pokud používáte federaci se službou AD FS (AD FS), musíte povolit synchronizaci hash hesel. (Doporučené nastavení naleznete v [tomto videu](https://youtu.be/qQruArbu2Ew).) Synchronizace hash hesel pomáhá s zotavením po havárii v případě selhání infrastruktury služby AD FS a také pomáhá poskytovat ochranu před únikem přihlašovacích údajů. Další informace najdete [v tématu Povolení synchronizace hash hesel se synchronizací Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

Použití místní služby Active Directory nebo služby Active Directory pouze na virtuálních počítačích IaaS, bez Azure AD a Azure AD DS, není podporovaná konfigurace pro clustery HDInsight s ESP.

Pokud se používá federace a hashy hesel jsou synchronizovány správně, ale dochází k chybám ověřování, zkontrolujte, jestli je pro instanční objekt služby Prostředí PowerShell povoleno ověřování pomocí hesla v cloudu. Pokud ne, musíte nastavit [zásadu zjišťování domácí sféry (HRD)](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) pro vašeho klienta Azure AD. Kontrola a nastavení zásad hrd:

1. Nainstalujte [modul Preview Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

   ```powershell
   Install-Module AzureAD
   ```

2. Připojte se pomocí pověření globálního správce (správce klienta).

   ```powershell
   Connect-AzureAD
   ```

3. Zkontrolujte, jestli už byl vytvořen instanční objekt Microsoft Azure PowerShell.

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

- [Konfigurace clusterů HDInsight pomocí protokolu ESP](apache-domain-joined-configure-using-azure-adds.md)
- [Konfigurace zásad Apache Hive pro clustery HDInsight pomocí protokolu ESP](apache-domain-joined-run-hive.md)
- [Správa clusterů HDInsight pomocí ESP](apache-domain-joined-manage.md)
