---
title: Podrobnosti o dodržování předpisů pro CIS Microsoft Azure Foundation – srovnávací 1.3.0
description: Podrobnosti o 1.3.0 základech standardů dodržování předpisů v modelu Microsoft Azure CI Každý ovládací prvek je namapován na jednu nebo více Azure Policy definic, které pomáhají s posouzením.
ms.date: 03/24/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: e359ae66e8dc34032e365af360020844ec8f66b8
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105032476"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-130-regulatory-compliance-built-in-initiative"></a>Podrobnosti o integrované iniciativě 1.3.0 Foundation podle standardů dodržování předpisů v modelu Microsoft Azure CI

Následující článek podrobně popisuje, jak Azure Policy definice předdefinovaného dodržování legislativních předpisů namapuje na domény a **ovládací prvky** **dodržování předpisů** v CIS Microsoft Azure Foundation test 1.3.0.
Další informace o tomto standardu dodržování předpisů najdete v článku [Microsoft Azure CIS 1.3.0 Foundation test](https://www.cisecurity.org/benchmark/azure/). Pokud chcete pochopit _vlastnictví_, přečtěte si téma [definice zásad Azure Policy](../concepts/definition-structure.md#type) a [sdílená odpovědnost v cloudu](../../../security/fundamentals/shared-responsibility.md).

Následující mapování jsou pro 1.3.0 ovládací prvky služby CI v rámci služby **ci Microsoft Azure Foundation** . Pomocí navigace na pravé straně můžete přejít přímo ke konkrétní **doméně dodržování předpisů**. Mnohé z ovládacích prvků jsou implementovány s definicí [Azure Policy](../overview.md) iniciativou. Chcete-li zkontrolovat definici kompletní iniciativy, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** .
Pak vyhledejte a vyberte definici služby **Microsoft Azure CIS 1.3.0 Foundation** pro dodržování předpisů, která je integrovaná v rámci předdefinovaných iniciativ.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../overview.md) .
> Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../how-to/get-compliance-data.md) pomocí ovládacího prvku. často však není 1:1 nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **se v Azure Policy** vztahuje pouze na samotné definice zásad; Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard kompatibility zahrnuje i ovládací prvky, které nejsou v tuto chvíli řešeny žádnými definicemi Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi doménami dodržování předpisů, ovládacími prvky a definicemi Azure Policy pro tento standard dodržování předpisů se mohou v průběhu času měnit. Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_3_0.json).

## <a name="identity-and-access-management"></a>Správa identit a přístupu

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Ujistěte se, že je u všech privilegovaných uživatelů povolené vícefaktorové ověřování.

**ID**: CIS Azure 1,1 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro účty s oprávněními k zápisu v předplatném by se mělo povolit MFA.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) by mělo být povolené pro všechny účty předplatného s oprávněním k zápisu, aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) by mělo být povolené pro všechny účty předplatného s oprávněním vlastníka, aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Ujistěte se, že je u všech neprivilegovaných uživatelů povolené vícefaktorové ověřování.

**ID**: CIS Azure 1,2 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |U všech účtů předplatného s oprávněním ke čtení by se mělo povolit Multi-Factor Authentication (MFA), aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-guest-users-are-reviewed-on-a-monthly-basis"></a>Zajistěte, aby se uživatelé typu Host zkontrolovali měsíčně.

**ID**: CIS Azure 1,3 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Aby nedocházelo k nemonitorovanému přístupu, měly by se z předplatného odebrat externí účty s oprávněním vlastníka. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Aby nedocházelo k nemonitorovanému přístupu, měly by se z předplatného odebrat externí účty s oprávněním ke čtení. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Z vašeho předplatného byste měli odebrat externí účty s oprávněním k zápisu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |K tomu, aby se zabránilo nemonitorovanému přístupu, měly by se z předplatného odebrat externí účty s oprávněním k zápisu. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Zajistěte, aby nevytvářely žádné vlastní role vlastníka předplatného.

**ID**: CIS Azure 1,21 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Vlastní role vlastníka předplatného by neměly existovat.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Tato zásada zajišťuje, že žádné vlastní role vlastníka předplatného neexistují. |Audit, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Security Center

### <a name="ensure-that-azure-defender-is-set-to-on-for-servers"></a>Ujistěte se, že je v Azure Defenderu pro servery nastavené na zapnuto.

**ID**: CIS Azure 2,1 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V Azure Defenderu by se měly povolit servery.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |Azure Defender pro servery poskytuje ochranu před hrozbami v reálném čase pro úlohy serveru a generuje doporučení pro posílení zabezpečení a také výstrahy týkající se podezřelých aktivit. |AuditIfNotExists, zakázáno |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-app-service"></a>Ujistěte se, že je v Azure Defenderu zapnutá sada pro App Service

**ID**: CIS Azure 2,2 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Je potřeba povolit Azure Defender pro App Service.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender pro App Service využívá škálování cloudu a viditelnost, kterou má Azure jako poskytovatel cloudu, ke sledování běžných útoků na webové aplikace. |AuditIfNotExists, zakázáno |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-azure-sql-database-servers"></a>Ujistěte se, že je v Azure Defenderu nastavené na zapnuto pro servery Azure SQL Database.

**ID**: CIS Azure 2,3 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Je potřeba povolit Azure Defender pro Azure SQL Database servery.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender pro SQL poskytuje funkce pro zpřístupnění a zmírnění potenciálních chyb zabezpečení databáze a zjišťuje neobvyklé aktivity, které by mohly označovat hrozby pro databáze SQL, a zjišťování a klasifikaci citlivých dat. |AuditIfNotExists, zakázáno |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-sql-servers-on-machines"></a>Zajistěte, aby byl v systému SQL Server na počítačích zapnutý Azure Defender.

**ID**: CIS Azure 2,4 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Je potřeba povolit Azure Defender pro SQL servery na počítačích.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender pro SQL poskytuje funkce pro zpřístupnění a zmírnění potenciálních chyb zabezpečení databáze a zjišťuje neobvyklé aktivity, které by mohly označovat hrozby pro databáze SQL, a zjišťování a klasifikaci citlivých dat. |AuditIfNotExists, zakázáno |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-storage"></a>Zajistěte, aby byl v Azure Defenderu pro úložiště zapnutý.

**ID**: CIS Azure 2,5 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Je potřeba povolit Azure Defender pro úložiště.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender pro úložiště poskytuje detekci neobvyklých a potenciálně škodlivých pokusů o přístup k účtům úložiště nebo jejich zneužití. |AuditIfNotExists, zakázáno |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-kubernetes"></a>Ujistěte se, že je v Azure Defenderu zapnutá sada pro Kubernetes.

**ID**: CIS Azure 2,6 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Je potřeba povolit Azure Defender pro Kubernetes.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender pro Kubernetes poskytuje ochranu před hrozbami v reálném čase pro kontejnerová prostředí a generuje výstrahy pro podezřelé aktivity. |AuditIfNotExists, zakázáno |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-container-registries"></a>Ujistěte se, že je v Azure Defenderu pro Registry kontejnerů nastavená zapnutá.

**ID**: CIS Azure 2,7 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V Azure Defenderu by se měly povolit Registry kontejnerů.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |Azure Defender pro Registry kontejnerů poskytuje kontrolu ohrožení zabezpečení všech imagí vydaných během posledních 30 dnů, vložení do registru nebo naimportování a zpřístupňuje podrobné nálezy na bitovou kopii. |AuditIfNotExists, zakázáno |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-key-vault"></a>Ujistěte se, že je v Azure Defenderu zapnutá sada pro Key Vault

**ID**: CIS Azure 2,8 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Je potřeba povolit Azure Defender pro Key Vault.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender pro Key Vault poskytuje další úroveň ochrany a přehledy zabezpečení díky detekci neobvyklých a potenciálně škodlivých pokusů o přístup k účtům trezoru klíčů nebo jejich zneužití. |AuditIfNotExists, zakázáno |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Ujistěte se, že je Automatické zřizování agenta monitorování nastavené na zapnuto.

**ID**: CIS Azure 2,11 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V předplatném by mělo být povolené Automatické zřizování agenta Log Analytics.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |K monitorování ohrožení zabezpečení a hrozeb Azure Security Center shromažďuje data z virtuálních počítačů Azure. Data shromažďuje agent Log Analytics, dříve označovaný jako Microsoft Monitoring Agent (MMA), který čte různé konfigurace a protokoly událostí související se zabezpečením z počítače a kopíruje data do vašeho pracovního prostoru Log Analytics pro účely analýzy. Doporučujeme povolit Automatické zřizování pro automatické nasazení agenta na všechny podporované virtuální počítače Azure a všechny nově vytvořené. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-additional-email-addresses-is-configured-with-a-security-contact-email"></a>Zajistěte, aby se pro další e-mailové adresy nakonfigurovala e-mailová kontaktní adresa

**ID**: CIS Azure 2,13 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Odběry by měly mít kontaktní e-mailovou adresu pro problémy se zabezpečením.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |Pokud chcete zajistit, aby se vaši uživatelé ve vaší organizaci dostali v případě potenciálního narušení zabezpečení v jednom z vašich předplatných, nastavte u kontaktu zabezpečení příjem e-mailových oznámení od Security Center. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-notify-about-alerts-with-the-following-severity-is-set-to-high"></a>Ujistěte se, že upozornění na výstrahy s následující závažností je nastavené na vysoká.

**ID**: CIS Azure 2,14 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Mělo by být povolené e-mailové oznámení pro výstrahy s vysokou závažností.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |Pokud chcete zajistit, aby se vaši uživatelé ve vaší organizaci dostali v případě potenciálního narušení zabezpečení v jednom z vašich předplatných, povolte pro výstrahy s vysokou závažností v Security Center e-mailová oznámení. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

## <a name="storage-accounts"></a>Účty úložiště

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Ujistěte se, že je požadovaný zabezpečený přenos nastavený na povoleno.

**ID**: CIS Azure 3,1 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Měl by se povolit zabezpečený přenos do účtů úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Audit požadavek na zabezpečený přenos v účtu úložiště. Zabezpečený přenos je možnost, která vynutí, aby váš účet úložiště přijímal požadavky jenom od zabezpečených připojení (HTTPS). Použití protokolu HTTPS zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako je třeba prostředník, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-that-public-access-level-is-set-to-private-for-blob-containers"></a>Zajistěte, aby úroveň veřejného přístupu byla nastavena na hodnotu Private pro kontejnery objektů BLOB.

**ID**: CIS Azure 3,5 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Veřejný přístup k účtu úložiště by neměl být povolený.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Anonymní veřejný přístup pro čtení kontejnerů a objektů BLOB v Azure Storage je pohodlný způsob, jak sdílet data, ale může představovat bezpečnostní riziko. Microsoft doporučuje zabránit veřejnému přístupu k účtu úložiště, pokud to nezpůsobí, že by porušení dat způsobila Nepožadovaná anonymní přístupová práva. |audit, zamítnutí, zakázáno |[2.0.1 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Ujistěte se, že výchozí pravidlo přístupu k síti pro účty úložiště je nastavené na odepřít.

**ID**: CIS Azure 3,6 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Účty úložiště by měly omezovat síťový přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Přístup k síti účtů úložiště by měl být omezený. Nakonfigurujte Síťová pravidla, aby k účtu úložiště měly přístup jenom aplikace z povolených sítí. Aby bylo možné připojení z určitých internetových nebo místních klientů, přístup k provozu z konkrétních virtuálních sítí Azure nebo do rozsahů veřejných IP adres se může udělit. |Audit, zamítnutí, zakázáno |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[Účty úložiště by měly omezovat přístup k síti pomocí pravidel virtuální sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |Chraňte svoje účty úložiště před potenciálními hrozbami pomocí pravidel virtuální sítě jako upřednostňovanou metodu namísto filtrování založeného na protokolu IP. Vypnutí filtrování na základě IP adres brání veřejné IP adrese v přístupu k účtům úložiště. |Audit, zamítnutí, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Zajistěte, aby se pro přístup k účtu úložiště povolily důvěryhodné služby Microsoftu.

**ID**: CIS Azure 3,7 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Účty úložiště by měly umožňovat přístup z důvěryhodných služeb Microsoftu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Některé služby Microsoftu, které pracují s účty úložiště, fungují ze sítí, které nemůžou udělit přístup prostřednictvím síťových pravidel. Aby mohl tento typ služby fungovat podle účelu, umožněte sadě důvěryhodných služeb společnosti Microsoft obejít Síťová pravidla. Tyto služby potom použijí silné ověřování pro přístup k účtu úložiště. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

### <a name="ensure-storage-for-critical-data-are-encrypted-with-customer-managed-key"></a>Ujistěte se, že úložiště pro kritická data jsou šifrovaná pomocí zákaznického spravovaného klíče.

**ID**: CIS Azure 3,9 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Účty úložiště by měly pro šifrování používat klíč spravovaný zákazníkem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fac406b-40ca-413b-bf8e-0bf964659c25) |Zabezpečte svůj účet úložiště větší flexibilitou pomocí klíčů spravovaných zákazníkem. Když zadáte klíč spravovaný zákazníkem, použije se tento klíč k ochraně a řízení přístupu ke klíči, který šifruje vaše data. Použití klíčů spravovaných zákazníkem poskytuje další možnosti pro řízení rotace klíčového šifrovacího klíče nebo kryptografického mazání dat. |Audit, zakázáno |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountCustomerManagedKeyEnabled_Audit.json) |

## <a name="database-services"></a>Databázové služby

### <a name="ensure-that-auditing-is-set-to-on"></a>Zajistěte, aby bylo auditování nastaveno na zapnuto.

**ID**: CIS Azure 4.1.1 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Auditování na SQL serveru by mělo být povolené.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Auditování v SQL Server by mělo být povoleno ke sledování databázových činností napříč všemi databázemi na serveru a jejich uložení v protokolu auditu. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Zajistěte, aby bylo šifrování dat u SQL Database nastaveno na zapnuto.

**ID**: SNS – **vlastnictví** Azure 4.1.2: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Je třeba povolit transparentní šifrování dat databází SQL.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Transparentní šifrování dat by mělo být povoleno pro ochranu neaktivních dat a splnění požadavků na dodržování předpisů. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Zajistěte, aby uchování auditování bylo větší než 90 dní.

**ID**: SNS – **vlastnictví** Azure 4.1.3: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[SQL servery s auditováním pro cíl účtu úložiště by měly být nakonfigurované s 90 dní uchovávání nebo vyšší úrovně.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Pro účely vyšetřování incidentů doporučujeme nastavit uchovávání dat pro auditování SQL Server k cílovému účtu úložiště aspoň na 90 dní. Potvrďte, že splňujete nezbytná pravidla uchovávání pro oblasti, ve kterých pracujete. V některých případech se vyžaduje dodržování regulativních standardů. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-threat-protection-atp-on-a-sql-server-is-set-to-enabled"></a>Zajistěte, aby byla rozšířená ochrana před internetovými útoky (ATP) na SQL serveru nastavená na povoleno.

**ID**: SNS – **vlastnictví** Azure 4.2.1: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Auditujte všechny spravované instance SQL bez pokročilých zabezpečení dat. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Auditovat SQL servery bez pokročilých zabezpečení dat |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-vulnerability-assessment-va-is-enabled-on-a-sql-server-by-setting-a-storage-account"></a>Zajistěte, aby bylo na SQL serveru povolené posouzení ohrožení zabezpečení (VA) nastavením účtu úložiště.

**ID**: CIS **vlastnictví** Azure 4.2.2: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Posouzení ohrožení zabezpečení by mělo být povoleno na spravované instanci SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Auditovat všechny spravované instance SQL, u kterých nejsou povolené kontroly opakovaného posuzování ohrožení zabezpečení Posouzení ohrožení zabezpečení může zjišťovat, sledovat a pomáhat při nápravě potenciálních ohrožení zabezpečení databáze. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[Na vašich serverech SQL by mělo být povolené posouzení ohrožení zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Auditovat servery Azure SQL, u kterých nejsou povolené kontroly opakovaného posuzování ohrožení zabezpečení Posouzení ohrožení zabezpečení může zjišťovat, sledovat a pomáhat při nápravě potenciálních ohrožení zabezpečení databáze. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |

### <a name="ensure-that-va-setting-send-scan-reports-to-is-configured-for-a-sql-server"></a>Ujistěte se, že nastavení VA Odeslat sestavy pro kontrolu je nakonfigurované pro SQL Server.

**ID**: SNS – **vlastnictví** Azure 4.2.4: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Nastavení posouzení ohrožení zabezpečení pro SQL Server by mělo obsahovat e-mailovou adresu pro příjem sestav kontroly.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057d6cfe-9c4f-4a6d-bc60-14420ea1f1a9) |Ujistěte se, že je k dispozici e-mailová adresa pro pole Odeslat kontrolu sestav do v nastavení posouzení ohrožení zabezpečení. Tato e-mailová adresa obdrží Shrnutí výsledků kontroly po pravidelné kontrole na serverech SQL. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_VulnerabilityAssessmentEmails_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Zajistěte, aby se vynutilo připojení SSL u databázového serveru PostgreSQL nastavené na povoleno.

**ID**: SNS – **vlastnictví** Azure 4.3.1: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL podporuje připojení Azure Database for MySQLho serveru k klientským aplikacím pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž" v prostředních prostředcích, a to šifrováním datového proudu mezi serverem a vaší aplikací. Tato konfigurace vynutila, že protokol SSL má vždycky povolený přístup k databázovému serveru. |Audit, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Zajistěte, aby se vynutilo připojení SSL u databázového serveru MySQL nastavené na povoleno.

**ID**: CIS **vlastnictví** Azure 4.3.2: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro databázové servery PostgreSQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL podporuje připojení Azure Database for PostgreSQLho serveru k klientským aplikacím pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž" v prostředních prostředcích, a to šifrováním datového proudu mezi serverem a vaší aplikací. Tato konfigurace vynutila, že protokol SSL má vždycky povolený přístup k databázovému serveru. |Audit, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Zajistěte, aby parametr serveru ' log_checkpoints ' byl pro databázový server PostgreSQL nastaven na hodnotu ' ON '.

**ID**: SNS – **vlastnictví** Azure 4.3.3: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro databázové servery PostgreSQL by měly být povoleny kontrolní body protokolu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Tato zásada pomáhá auditovat všechny databáze PostgreSQL ve vašem prostředí, aniž by bylo povolené nastavení log_checkpoints. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Zajistěte, aby parametr serveru ' log_connections ' byl pro databázový server PostgreSQL nastaven na hodnotu ' ON '.

**ID**: CIS Azure 4.3.4 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro databázové servery PostgreSQL by se měla povolit protokolovat připojení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Tato zásada pomáhá auditovat všechny databáze PostgreSQL ve vašem prostředí, aniž by bylo povolené nastavení log_connections. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Zajistěte, aby parametr serveru ' log_disconnections ' byl pro databázový server PostgreSQL nastaven na hodnotu ' ON '.

**ID**: CIS Azure 4.3.5 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Odpojení by se měla protokolovat pro databázové servery PostgreSQL.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Tato zásada pomáhá auditovat všechny databáze PostgreSQL ve vašem prostředí, aniž by bylo povoleno log_disconnections. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Zajistěte, aby parametr serveru ' connection_throttling ' byl pro databázový server PostgreSQL nastaven na hodnotu ' ON '.

**ID**: CIS Azure 4.3.6 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro databázové servery PostgreSQL by mělo být povoleno omezování připojení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Tato zásada pomáhá auditovat všechny databáze PostgreSQL ve vašem prostředí bez povoleného omezení připojení. Toto nastavení umožňuje dočasné omezení počtu připojení na IP adresu pro příliš mnoho neplatných neúspěšných přihlášení k heslům. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Ujistěte se, že je nakonfigurovaný správce Azure Active Directory.

**ID**: CIS Azure 4,4 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro SQL servery by se měl zřídit správce Azure Active Directory.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Proveďte audit zřizování správce Azure Active Directory pro SQL Server, aby se povolilo ověřování Azure AD. Ověřování Azure AD umožňuje zjednodušenou správu oprávnění a centralizovanou správu identit uživatelů databáze a dalších služeb Microsoftu. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-customer-managed-key"></a>Zajistěte, aby ochrana TDE systému SQL Server byla zašifrovaná pomocí klíče spravovaného zákazníkem.

**ID**: CIS Azure 4,5 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravované instance SQL by měly používat klíče spravované zákazníkem k šifrování neaktivních dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Implementace transparentní šifrování dat (TDE) s vlastním klíčem vám poskytne větší transparentnost a kontrolu nad ochranným prostředím TDE, zvýšené zabezpečení pomocí externí služby založené na HSM a povýšení oddělení povinností. Toto doporučení se vztahuje na organizace s příslušným požadavkem na dodržování předpisů. |AuditIfNotExists, zakázáno |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL servery by se měly používat pro šifrování neaktivních dat pomocí klíčů spravovaných zákazníkem.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Implementace transparentní šifrování dat (TDE) s vlastním klíčem zajišťuje větší transparentnost a kontrolu nad ochranným prostředím TDE, zvýšené zabezpečení pomocí externí služby založené na HSM a povýšení oddělení povinností. Toto doporučení se vztahuje na organizace s příslušným požadavkem na dodržování předpisů. |AuditIfNotExists, zakázáno |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

### <a name="ensure-the-storage-container-storing-the-activity-logs-is-not-publicly-accessible"></a>Zajistěte, aby kontejner úložiště, který ukládá protokoly aktivit, nebyl veřejně přístupný.

**ID**: CIS **vlastnictví** Azure 5.1.3: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Veřejný přístup k účtu úložiště by neměl být povolený.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Anonymní veřejný přístup pro čtení kontejnerů a objektů BLOB v Azure Storage je pohodlný způsob, jak sdílet data, ale může představovat bezpečnostní riziko. Microsoft doporučuje zabránit veřejnému přístupu k účtu úložiště, pokud to nezpůsobí, že by porušení dat způsobila Nepožadovaná anonymní přístupová práva. |audit, zamítnutí, zakázáno |[2.0.1 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Zajistěte, aby byl účet úložiště obsahující kontejner s protokoly aktivit zašifrovaný pomocí BYOK (použijte vlastní klíč).

**ID**: SNS – **vlastnictví** Azure 5.1.4: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Účet úložiště obsahující kontejner s protokoly aktivit musí být zašifrovaný pomocí BYOK.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Tato zásada Audituje, jestli je účet úložiště obsahující kontejner s protokoly aktivit zašifrovaný pomocí BYOK. Zásada funguje jenom v případě, že účet úložiště se nachází ve stejném předplatném jako protokoly aktivit podle návrhu. Další informace o šifrování Azure Storage v klidovém umístění najdete tady [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok) .  |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Ujistěte se, že protokolování služby Azure webrecovery je povolené.

**ID**: CIS **vlastnictví** Azure 5.1.5: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Měly by být povolené protokoly prostředků v Key Vault.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Audit povolování protokolů prostředků. Díky tomu můžete znovu vytvořit stopy aktivity, které se použijí pro účely šetření, když dojde k bezpečnostnímu incidentu nebo když dojde k ohrožení zabezpečení sítě. |AuditIfNotExists, zakázáno |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Ujistěte se, že existuje výstraha protokolu aktivit pro vytvoření přiřazení zásady.

**ID**: SNS – **vlastnictví** Azure 5.2.1: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace zásad by mělo existovat upozornění protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Tato zásada Audituje konkrétní operace zásad bez nakonfigurovaného nastavení výstrahy protokolu aktivit. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-policy-assignment"></a>Zajistěte, aby pro odstranění přiřazení zásad existovalo upozornění protokolu aktivit.

**ID**: SNS – **vlastnictví** Azure 5.2.2: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace zásad by mělo existovat upozornění protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Tato zásada Audituje konkrétní operace zásad bez nakonfigurovaného nastavení výstrahy protokolu aktivit. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Zajistěte, aby pro vytvoření nebo aktualizaci skupiny zabezpečení sítě existovalo upozornění protokolu aktivit.

**ID**: SNS – **vlastnictví** Azure 5.2.3: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace správy by měla existovat výstraha protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Tato zásada Audituje konkrétní operace správy bez nakonfigurovaného nastavení výstrahy protokolu aktivit. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Zajistěte, aby pro odstranění skupiny zabezpečení sítě existovalo upozornění protokolu aktivit.

**ID**: SNS – **vlastnictví** Azure 5.2.4: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace správy by měla existovat výstraha protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Tato zásada Audituje konkrétní operace správy bez nakonfigurovaného nastavení výstrahy protokolu aktivit. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Zajistěte, aby pro pravidlo vytvoření nebo aktualizace skupiny zabezpečení sítě existovalo upozornění protokolu aktivit.

**ID**: CIS Azure 5.2.5 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace správy by měla existovat výstraha protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Tato zásada Audituje konkrétní operace správy bez nakonfigurovaného nastavení výstrahy protokolu aktivit. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Zajistěte, aby pro odstranění pravidla skupiny zabezpečení sítě existovalo upozornění protokolu aktivit.

**ID**: CIS Azure 5.2.6 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace správy by měla existovat výstraha protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Tato zásada Audituje konkrétní operace správy bez nakonfigurovaného nastavení výstrahy protokolu aktivit. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Zajistěte, aby pro vytvoření nebo aktualizaci řešení zabezpečení existovalo upozornění protokolu aktivit.

**ID**: CIS Azure 5.2.7 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace zabezpečení by mělo existovat upozornění protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Tato zásada Audituje konkrétní operace zabezpečení bez nakonfigurovaného upozornění protokolu aktivit. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Zajistěte, aby pro odstranění řešení zabezpečení existovalo upozornění protokolu aktivit.

**ID**: CIS Azure 5.2.8 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace zabezpečení by mělo existovat upozornění protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Tato zásada Audituje konkrétní operace zabezpečení bez nakonfigurovaného upozornění protokolu aktivit. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Zajistěte, aby pro pravidlo brány firewall pro vytvoření nebo aktualizaci nebo odstranění SQL Server existovalo upozornění protokolu aktivit.

**ID**: CIS Azure 5.2.9 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace správy by měla existovat výstraha protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Tato zásada Audituje konkrétní operace správy bez nakonfigurovaného nastavení výstrahy protokolu aktivit. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-diagnostic-logs-are-enabled-for-all-services-which-support-it"></a>Zajistěte, aby byly diagnostické protokoly povolené pro všechny služby, které je podporují.

**ID**: CIS Azure 5,3 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Měly by být povolené diagnostické protokoly v App Services.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Audituje povolení diagnostických protokolů v aplikaci. Díky tomu můžete znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[Měly by být povolené protokoly prostředků v Azure Data Lake Store.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Audit povolování protokolů prostředků. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Měly by být povolené protokoly prostředků v Azure Stream Analytics.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Audit povolování protokolů prostředků. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Protokoly prostředků v účtech Batch by se měly povolit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Audit povolování protokolů prostředků. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Měly by být povolené protokoly prostředků v Data Lake Analytics.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Audit povolování protokolů prostředků. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[Protokoly prostředků v centru událostí by měly být povolené.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Audit povolování protokolů prostředků. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[Měly by být povolené protokoly prostředků v IoT Hub.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Audit povolování protokolů prostředků. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[Měly by být povolené protokoly prostředků v Key Vault.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Audit povolování protokolů prostředků. Díky tomu můžete znovu vytvořit stopy aktivity, které se použijí pro účely šetření, když dojde k bezpečnostnímu incidentu nebo když dojde k ohrožení zabezpečení sítě. |AuditIfNotExists, zakázáno |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[Měly by být povolené protokoly prostředků v Logic Apps.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Audit povolování protokolů prostředků. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[Protokoly prostředků ve vyhledávacích službách by se měly povolit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Audit povolování protokolů prostředků. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Měly by být povolené protokoly prostředků v Service Bus.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Audit povolování protokolů prostředků. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Měly by být povolené protokoly prostředků v Virtual Machine Scale Sets.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Doporučuje se povolit protokoly, aby se záznam aktivity mohl znovu vytvořit, když se v případě incidentu nebo ohrožení vyžaduje šetření. |AuditIfNotExists, zakázáno |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

## <a name="networking"></a>Sítě

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>Zajistěte, aby byl přístup protokolu RDP omezen z Internetu.

**ID**: CIS Azure 6,1 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Přístup RDP z Internetu by měl být blokovaný.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Tato zásada Audituje jakékoli pravidlo zabezpečení sítě, které umožňuje přístup k protokolu RDP z Internetu. |Audit, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>Zajistěte, aby přístup přes protokol SSH byl omezen z Internetu.

**ID**: CIS Azure 6,2 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Přístup přes SSH z Internetu by měl být zablokovaný.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Tato zásada Audituje jakékoli pravidlo zabezpečení sítě, které umožňuje přístup přes SSH z Internetu. |Audit, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Ujistěte se, že Network Watcher je povolené.

**ID**: CIS Azure 6,5 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Network Watcher by měl být povolený.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher je místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni síťového scénáře v, do a z Azure. Monitorování úrovně scénáře umožňuje diagnostikovat problémy v zobrazení na úrovni koncových sítí. Nástroje pro diagnostiku a diagnostiku sítě, které jsou dostupné v Network Watcher, vám pomůžou pochopit, diagnostikovat a získávat přehledy vaší sítě v Azure. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Virtual Machines

### <a name="ensure-virtual-machines-are-utilizing-managed-disks"></a>Ujistěte se, že Virtual Machines využívá Managed Disks

**ID**: CIS Azure 7,1 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Auditovat virtuální počítače, které nepoužívají spravované disky](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Tato zásada Audituje virtuální počítače, které nepoužívají spravované disky. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |

### <a name="ensure-that-os-and-data-disks-are-encrypted-with-cmk"></a>Zajistěte, aby byl operační systém a data zašifrované pomocí CMK.

**ID**: CIS Azure 7,2 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Na virtuálních počítačích by se mělo použít šifrování disku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Virtuální počítače bez povoleného šifrování disku se monitorují Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted-with-cmk"></a>Zajistěte, aby byly nepřipojené disky v CMK šifrované.

**ID**: CIS Azure 7,3 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Nepřipojené disky by měly být zašifrované](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Tato zásada Audituje všechny nepřipojené disky bez povoleného šifrování. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Zajistěte, aby byla nainstalovaná jenom schválená rozšíření.

**ID**: CIS Azure 7,4 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Musí být nainstalovaná jenom schválená rozšíření virtuálních počítačů.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Tato zásada řídí rozšíření virtuálních počítačů, která nejsou schválena. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Zajistěte, aby se používaly nejnovější opravy operačního systému pro všechny Virtual Machines.

**ID**: CIS Azure 7,5 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Na počítače by se měly nainstalovat aktualizace systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Chybějící aktualizace systému zabezpečení na vašich serverech se monitorují Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Ujistěte se, že je nainstalovaná ochrana koncového bodu pro všechny Virtual Machines.

**ID**: CIS Azure 7,6 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Monitorovat chybějící Endpoint Protection v Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servery bez nainstalovaného agenta Endpoint Protection se budou monitorovat Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Další informace o zabezpečení

### <a name="ensure-that-the-expiration-date-is-set-on-all-keys"></a>Ujistěte se, že datum vypršení platnosti je nastavené u všech klíčů.

**ID**: CIS Azure 8,1 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Key Vault klíče by měly mít datum vypršení platnosti.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |Kryptografické klíče by měly mít definované datum vypršení platnosti a nesmí být trvalé. Klíče, které jsou trvale platné, poskytují potenciálnímu útočníkovi více času na zabezpečení klíče. Pro nastavení dat vypršení platnosti kryptografických klíčů je doporučený postup zabezpečení. |Audit, zamítnutí, zakázáno |[1.0.1 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |

### <a name="ensure-that-the-expiration-date-is-set-on-all-secrets"></a>Ujistěte se, že datum vypršení platnosti je nastavené u všech tajných kódů.

**ID**: CIS Azure 8,2 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Key Vault tajných kódů by mělo mít datum vypršení platnosti.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |Tajné kódy by měly mít definované datum vypršení platnosti a nesmí být trvalé. Tajné kódy, které jsou platné trvale, poskytují potenciálnímu útočníkovi více času na jejich zabezpečení. Pro nastavení dat vypršení platnosti tajných kódů je doporučený postup zabezpečení. |Audit, zamítnutí, zakázáno |[1.0.1 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |

### <a name="ensure-the-key-vault-is-recoverable"></a>Ujistěte se, že je Trezor klíčů obnovitelný.

**ID**: CIS Azure 8,4 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Trezory klíčů by měly mít povolenou ochranu vyprázdnění](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Zlomyslné odstranění trezoru klíčů může vést k trvalé ztrátě dat. Škodlivý program Insider ve vaší organizaci může potenciálně odstraňovat a vyprázdnit trezory klíčů. Vyčištění ochrany chrání vás před útoky z programu Insider tím, že vynucuje povinnou dobu uchování pro tiché odstraněné trezory klíčů. Nikdo ve vaší organizaci ani Microsoft nebude moct vyprázdnit vaše trezory klíčů během doby uchovávání obnovitelného odstranění. |Audit, zamítnutí, zakázáno |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Povolení řízení přístupu na základě role (RBAC) v rámci služeb Azure Kubernetes Services

**ID**: CIS Azure 8,5 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro služby Kubernetes by se měla použít Access Control na základě rolí (RBAC).](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |K zajištění podrobného filtrování akcí, které mohou uživatelé provádět, použijte Role-Based Access Control (RBAC) ke správě oprávnění v clusterech služby Kubernetes a ke konfiguraci relevantních zásad autorizace. |Audit, zakázáno |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="app-service"></a>App Service

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Ujistěte se, že je App Service ověřování nastaveno na Azure App Service

**ID**: CIS Azure 9,1 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V aplikaci API by mělo být povolené ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |Ověřování Azure App Service je funkce, která může zabránit tomu, aby anonymní požadavky HTTP dostaly do aplikace API nebo aby ověřovala ty, které mají tokeny před dosažením aplikace API. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[Ve vaší aplikaci Function app by mělo být povolené ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |Ověřování Azure App Service je funkce, která může zabránit tomu, aby anonymní požadavky HTTP dostaly do aplikace Function App nebo ověřovala ty, které mají tokeny předtím, než dosáhnou aplikace Function App. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[Ve vaší webové aplikaci by mělo být povolené ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |Ověřování Azure App Service je funkce, která může zabránit tomu, aby se anonymní požadavky HTTP dostaly do webové aplikace, nebo aby se ověřily, které mají tokeny před dosažením webové aplikace. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Ujistěte se, že webová aplikace přesměruje veškerý provoz HTTP na HTTPS v Azure App Service

**ID**: CIS Azure 9,2 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Webová aplikace by měla být přístupná jen přes protokol HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Zajistěte, aby webová aplikace používala nejnovější verzi šifrování TLS.

**ID**: CIS Azure 9,3 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V aplikaci API by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[V Function App by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Ve vaší webové aplikaci by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Ujistěte se, že má webová aplikace nastavené klientské certifikáty (příchozí klientské certifikáty) na zapnuto.

**ID**: CIS Azure 9,4 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Zajistěte, aby měla aplikace API pro klientské certifikáty (příchozí klientské certifikáty) nastavené na zapnuto.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |Klientské certifikáty umožňují, aby aplikace požádala o certifikát pro příchozí požadavky. Aplikaci budou moci kontaktovat pouze klienti s platným certifikátem. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Zajistěte, aby webová aplikace měla klientské certifikáty (příchozí klientské certifikáty) nastavené na zapnuto.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Klientské certifikáty umožňují, aby aplikace požádala o certifikát pro příchozí požadavky. Aplikaci budou moci kontaktovat pouze klienti s platným certifikátem. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[Aplikace Function app by měly mít povolené klientské certifikáty (příchozí klientské certifikáty).](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |Klientské certifikáty umožňují, aby aplikace požádala o certifikát pro příchozí požadavky. Pouze klienti s platnými certifikáty budou moci kontaktovat aplikaci. |Audit, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Ujistěte se, že je v App Service povolená možnost registrovat v Azure Active Directory.

**ID**: CIS Azure 9,5 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Ve vaší aplikaci API by se měla používat spravovaná identita.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Použití spravované identity pro rozšířené zabezpečení ověřování |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[Ve vašem Function App by se měla používat spravovaná identita.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Použití spravované identity pro rozšířené zabezpečení ověřování |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Ve vaší webové aplikaci by se měla použít spravovaná identita.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Použití spravované identity pro rozšířené zabezpečení ověřování |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Zajistěte, aby byla verze PHP nejnovější, pokud se používá ke spuštění webové aplikace.

**ID**: CIS Azure 9,6 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Zajistěte, aby byla verze PHP nejnovější, pokud se používá jako součást aplikace API.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |V důsledku chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze softwaru PHP. Použití nejnovější verze PHP pro aplikace API se doporučuje, aby bylo možné využívat opravy zabezpečení, pokud jsou nějaké nebo nové funkce nejnovější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Zajistěte, aby byla verze PHP nejnovější, pokud se používá jako součást webové aplikace.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |V důsledku chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze softwaru PHP. Použití nejnovější verze PHP pro webové aplikace se doporučuje, aby bylo možné využívat opravy zabezpečení, pokud jsou nějaké nebo nové funkce nejnovější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Zajistěte, aby byla nejnovější verze Pythonu, pokud se používá ke spuštění webové aplikace.

**ID**: CIS Azure 9,7 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Zajistěte, aby byla nejnovější verze Pythonu, pokud se používá jako součást aplikace API.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Z důvodu chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze softwaru Pythonu. Použití nejnovější verze Pythonu pro aplikace API se doporučuje, aby bylo možné využívat opravy zabezpečení, pokud jsou nějaké nebo nové funkce nejnovější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Zajistěte, aby byla nejnovější verze Pythonu, pokud se používá jako součást aplikace Function App.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Z důvodu chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze softwaru Pythonu. Použití nejnovější verze Pythonu pro aplikace Function App se doporučuje, aby bylo možné využívat opravy zabezpečení, pokud jsou nějaké nebo nové funkce nejnovější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Zajistěte, aby byla nejnovější verze Pythonu, pokud se používá jako součást webové aplikace.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Z důvodu chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze softwaru Pythonu. Použití nejnovější verze Pythonu pro Web Apps se doporučuje, aby bylo možné využívat opravy zabezpečení, pokud jsou nějaké nebo nové funkce nejnovější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Zajistěte, aby byl jazyk Java verze nejnovější, pokud se používá ke spuštění webové aplikace.

**ID**: CIS Azure 9,8 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Zajistěte, aby byl jazyk Java verze nejnovější, pokud se používá jako součást aplikace API.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Z důvodu chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze pro Java. Použití nejnovější verze Pythonu pro aplikace API se doporučuje, aby bylo možné využívat opravy zabezpečení, pokud jsou nějaké nebo nové funkce nejnovější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Zajistěte, aby byl jazyk Java verze nejnovější, pokud se používá jako součást aplikace Function App.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Z důvodu chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze pro software Java. Použití nejnovější verze Java pro aplikace Function App se doporučuje, aby bylo možné využívat opravy zabezpečení, pokud jsou nějaké nebo nové funkce nejnovější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Zajistěte, aby byl jazyk Java verze nejnovější, pokud se používá jako součást webové aplikace.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Z důvodu chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze pro software Java. Použití nejnovější verze Java pro webové aplikace se doporučuje, aby bylo možné využívat opravy zabezpečení, pokud jsou nějaké nebo nové funkce nejnovější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Ujistěte se, že hodnota HTTP verze je nejnovější, pokud se používá ke spuštění webové aplikace.

**ID**: CIS Azure 9,9 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Ujistěte se, že hodnota HTTP verze je nejnovější, pokud se používá ke spuštění aplikace API.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |Z důvodu chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze pro protokol HTTP. Využijte nejnovější verzi protokolu HTTP pro webové aplikace, abyste mohli využít opravy zabezpečení, pokud jsou nějaké nebo nové funkce novější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Ujistěte se, že hodnota HTTP Version je nejnovější, pokud se používá ke spuštění aplikace Function App.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |Z důvodu chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze pro protokol HTTP. Využijte nejnovější verzi protokolu HTTP pro webové aplikace, abyste mohli využít opravy zabezpečení, pokud jsou nějaké nebo nové funkce novější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Ujistěte se, že hodnota HTTP verze je nejnovější, pokud se používá ke spuštění webové aplikace.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Z důvodu chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze pro protokol HTTP. Využijte nejnovější verzi protokolu HTTP pro webové aplikace, abyste mohli využít opravy zabezpečení, pokud jsou nějaké nebo nové funkce novější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

### <a name="ensure-ftp-deployments-are-disabled"></a>Zajistěte, aby bylo nasazení FTP zakázané.

**ID**: CIS Azure 9,10 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V aplikaci API by se měla vyžadovat jenom FTPS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |Povolit vynucení FTPS pro rozšířené zabezpečení |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[FTPS by mělo být vyžadováno jenom v Function App](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |Povolit vynucení FTPS pro rozšířené zabezpečení |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[Ve vaší webové aplikaci by se mělo vyžadovat FTPS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |Povolit vynucení FTPS pro rozšířené zabezpečení |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |

> [!NOTE]
> Dostupnost konkrétních definic Azure Policy se může v Azure Government a dalších národních cloudech lišit.

## <a name="next-steps"></a>Další kroky

Další články o Azure Policy:

- Přehled [dodržování legislativních předpisů](../concepts/regulatory-compliance.md) .
- Podívejte se na [strukturu definice iniciativy](../concepts/initiative-definition-structure.md).
- Podívejte se na další příklady na [Azure Policy Samples](./index.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
