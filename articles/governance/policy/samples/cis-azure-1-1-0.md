---
title: Podrobnosti o dodržování předpisů pro CIS Microsoft Azure Foundation – srovnávací testy
description: Podrobnosti o integrované iniciativě pro dodržování předpisů v modelu CIS Microsoft Azure Foundation Každý ovládací prvek je namapován na jednu nebo více Azure Policy definic, které pomáhají s posouzením.
ms.date: 10/20/2020
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 06b74daff584c9df35d4c762c3115b95e4ed3179
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320595"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-regulatory-compliance-built-in-initiative"></a>Podrobnosti o integrované iniciativě pro dodržování předpisů v modelu CIS Microsoft Azure Foundation

Následující článek podrobně popisuje, jak Azure Policy integrovaná definice iniciativ dodržování legislativních předpisů mapuje na **domény** a **ovládací prvky** v CIS Microsoft Azure Foundation srovnávací test.
Další informace o tomto standardu dodržování předpisů najdete v článku věnovaném standardu [CIS Microsoft Azure Foundation](https://www.cisecurity.org/benchmark/azure/). Pokud chcete pochopit _vlastnictví_, přečtěte si téma [definice zásad Azure Policy](../concepts/definition-structure.md#type) a [sdílená odpovědnost v cloudu](../../../security/fundamentals/shared-responsibility.md).

Následující mapování jsou pro ovládací prvky **srovnávacích testů služby CIS Microsoft Azure Foundation** . Pomocí navigace na pravé straně můžete přejít přímo ke konkrétní **doméně dodržování předpisů**. Mnohé z ovládacích prvků jsou implementovány s definicí [Azure Policy](../overview.md) iniciativou. Chcete-li zkontrolovat definici kompletní iniciativy, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** .
Pak vyhledejte a vyberte definici služby **Microsoft Azure CIS 1.1.0 Foundation** pro dodržování předpisů, která je integrovaná v rámci předdefinovaných iniciativ.

Tato integrovaná iniciativa se nasadí jako součást [ukázky srovnávacího plánu pro CIS Microsoft Azure Foundation](../../blueprints/samples/cis-azure-1-1-0.md).

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../overview.md) .
> Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../how-to/get-compliance-data.md) pomocí ovládacího prvku. Nicméně často není jedna nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **se v Azure Policy** vztahuje pouze na samotné definice zásad; Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard kompatibility zahrnuje i ovládací prvky, které nejsou v tuto chvíli řešeny žádnými definicemi Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi doménami dodržování předpisů, ovládacími prvky a definicemi Azure Policy pro tento standard dodržování předpisů se mohou v průběhu času měnit. Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_1_0_audit.json).

## <a name="identity-and-access-management"></a>Správa identit a přístupu

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Ujistěte se, že je u všech privilegovaných uživatelů povolené vícefaktorové ověřování.

**ID**: CIS Azure 1,1 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro účty s oprávněními k zápisu v předplatném by se mělo povolit MFA.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) by mělo být povolené pro všechny účty předplatného s oprávněním k zápisu, aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) by mělo být povolené pro všechny účty předplatného s oprávněním vlastníka, aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Ujistěte se, že je u všech neprivilegovaných uživatelů povolené vícefaktorové ověřování.

**ID**: CIS Azure 1,2 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |U všech účtů předplatného s oprávněním ke čtení by se mělo povolit Multi-Factor Authentication (MFA), aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-that-there-are-no-guest-users"></a>Ujistěte se, že neexistují žádní uživatelé typu Host.

**ID**: CIS Azure 1,3 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Aby nedocházelo k nemonitorovanému přístupu, měly by se z předplatného odebrat externí účty s oprávněním vlastníka. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Aby nedocházelo k nemonitorovanému přístupu, měly by se z předplatného odebrat externí účty s oprávněním ke čtení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Z vašeho předplatného byste měli odebrat externí účty s oprávněním k zápisu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |K tomu, aby se zabránilo nemonitorovanému přístupu, měly by se z předplatného odebrat externí účty s oprávněním k zápisu. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Zajistěte, aby nevytvářely žádné vlastní role vlastníka předplatného.

**ID**: CIS Azure 1,23 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Vlastní role vlastníka předplatného by neměly existovat.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Tato zásada zajišťuje, že žádné vlastní role vlastníka předplatného neexistují. |Audit, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Security Center

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Ujistěte se, že je Automatické zřizování agenta monitorování nastavené na zapnuto.

**ID**: CIS Azure 2,2 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V předplatném by mělo být povolené Automatické zřizování agenta monitorování Log Analytics.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Povolení automatického zřizování agenta monitorování Log Analytics za účelem shromažďování dat zabezpečení |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>Zajistěte, aby nastavení výchozí zásady ASC "monitorované systémové aktualizace" nebylo zakázané.

**ID**: CIS Azure 2,3 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Na počítače by se měly nainstalovat aktualizace systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Chybějící aktualizace systému zabezpečení na vašich serverech se monitorují Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>Zajistěte, aby nastavení výchozích zásad ASC "monitorování slabých míst operačního systému" nebylo zakázané.

**ID**: CIS Azure 2,4 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Servery, které nesplňují nakonfigurované směrné plány, se budou monitorovat Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>Zajistěte, aby nastavení výchozí zásady ASC "monitor Endpoint Protection" nebylo zakázané.

**ID**: CIS Azure 2,5 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Monitorovat chybějící Endpoint Protection v Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servery bez nainstalovaného agenta Endpoint Protection se budou monitorovat Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>Zajistěte, aby nastavení výchozí zásady ASC "monitorované disky" nebylo zakázané.

**ID**: CIS Azure 2,6 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Na virtuálních počítačích by se mělo použít šifrování disku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Virtuální počítače bez povoleného šifrování disku se monitorují Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>Zajistěte, aby nastavení výchozí zásady ASC "monitorovat skupiny zabezpečení sítě" nebylo zakázané.

**ID**: CIS Azure 2,7 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Doporučení adaptivního posílení zabezpečení sítě by se měla použít na internetových virtuálních počítačích.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analyzuje modely přenosů internetových virtuálních počítačů a poskytuje doporučení pro pravidla skupiny zabezpečení sítě, která omezují potenciální útok na plochu. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>Zajistěte, aby nastavení výchozí zásady ASC "povolit bránu firewall nové generace (NGFW)" nebylo zakázané "

**ID**: CIS Azure 2,9 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Virtuální počítače s přístupem k Internetu by měly být chráněné pomocí skupin zabezpečení sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Chraňte své virtuální počítače před potenciálními hrozbami tím, že omezíte přístup k nim pomocí skupin zabezpečení sítě (NSG). Další informace o řízení provozu pomocí skupin zabezpečení sítě [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Podsítě by měly být přidružené ke skupině zabezpečení sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Chraňte svoji podsíť před potenciálními hrozbami tím, že omezíte přístup k ní pomocí skupiny zabezpečení sítě (NSG). Skupin zabezpečení sítě obsahují Access Control seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo zakazují síťový provoz do vaší podsítě. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>Zajistěte, aby nastavení výchozí zásady ASC "posouzení ohrožení zabezpečení" nebylo zakázané.

**ID**: CIS Azure 2,10 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F760a85ff-6162-42b3-8d70-698e268f648c) |Monitoruje chyby zabezpečení zjištěné řešením posouzení ohrožení zabezpečení a virtuální počítače bez řešení posouzení ohrožení zabezpečení v Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>Zajistěte, aby nastavení výchozí zásady ASC "monitorovat síťový přístup JIT" nebylo zakázané.

**ID**: CIS Azure 2,12 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Porty pro správu virtuálních počítačů by měly být chráněné pomocí řízení přístupu k síti za běhu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Přístup k síti je možný jenom v čase (JIT) se monitoruje Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>Zajistěte, aby nastavení výchozí zásady ASC "monitorování adaptivních aplikací" nebylo zakázané.

**ID**: CIS Azure 2,13 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V počítačích by měly být povolené Adaptivní řízení aplikací pro definování bezpečných aplikací.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Povolit ovládací prvky aplikace, které definují seznam známých bezpečných aplikací spuštěných na vašich počítačích, a upozorní vás, když se spouštějí jiné aplikace. To pomáhá posílit zabezpečení vašich počítačů proti malwaru. Aby bylo možné zjednodušit proces konfigurace a údržby pravidel, Security Center využívá Machine Learning k analýze aplikací spuštěných na jednotlivých počítačích a k návrhu seznamu známých aplikací, které jsou v bezpečí. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>Zajistěte, aby nastavení výchozí zásady ASC "monitor SQL audit" nebylo zakázané.

**ID**: CIS Azure 2,14 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Auditování na SQL serveru by mělo být povolené.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Auditování v SQL Server by mělo být povoleno ke sledování databázových činností napříč všemi databázemi na serveru, s výjimkou synapse a jejich uložení v protokolu auditu. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>Zajistěte, aby nastavení výchozí zásady ASC "monitor SQL Encryption" nebylo zakázané.

**ID**: CIS Azure 2,15 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Je třeba povolit transparentní šifrování dat databází SQL.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Transparentní šifrování dat by mělo být povoleno pro ochranu neaktivních dat a splnění požadavků na dodržování předpisů. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-security-contact-emails-is-set"></a>Ujistěte se, že je nastavená možnost e-maily kontaktů zabezpečení.

**ID**: CIS Azure 2,16 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro vaše předplatné by se měla zadat e-mailová adresa kontaktu pro zabezpečení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |Zadejte e-mailovou adresu pro příjem oznámení, když Azure Security Center zjistí napadené prostředky. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-security-contact-phone-number-is-set"></a>Ujistěte se, že je nastavená možnost kontakt na zabezpečení telefonní číslo.

**ID**: CIS Azure 2,17 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro vaše předplatné by se mělo zadat telefonní číslo kontaktu zabezpečení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4d66858-c922-44e3-9566-5cdb7a7be744) |Zadejte telefonní číslo, které bude dostávat oznámení, když Azure Security Center zjistí napadené prostředky. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_phone_number.json) |

### <a name="ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>Ujistěte se, že možnost Odeslat e-mailem upozornění s vysokou závažností je nastavená na zapnuto.

**ID**: CIS Azure 2,18 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Mělo by být povolené e-mailové oznámení pro výstrahy s vysokou závažností.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |Povolte e-mailem upozornění zabezpečení na kontakt zabezpečení, aby bylo možné přijímat e-maily s výstrahou zabezpečení od Microsoftu. Tím je zajištěno, že dopraví lidé vědí na případné problémy se zabezpečením a jsou schopni zmírnit rizika. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

### <a name="ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>Ujistěte se, že možnost Odeslat e-mail také vlastníkům předplatného je nastavená na zapnuto.

**ID**: CIS Azure 2,19 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Mělo by se povolit e-mailové oznámení vlastníkovi předplatného pro upozornění s vysokou závažností.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |Povolte e-mailem výstrahy zabezpečení pro vlastníka předplatného, aby mohli přijímat e-maily s výstrahami zabezpečení od Microsoftu. Tím se zajistí, že budou vědět o všech potenciálních problémech se zabezpečením a můžou se riziko zmírnit včas. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |

## <a name="storage-accounts"></a>Účty úložiště

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Ujistěte se, že je požadovaný zabezpečený přenos nastavený na povoleno.

**ID**: CIS Azure 3,1 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Měl by se povolit zabezpečený přenos do účtů úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Audit požadavek na zabezpečený přenos v účtu úložiště. Zabezpečený přenos je možnost, která vynutí, aby váš účet úložiště přijímal požadavky jenom od zabezpečených připojení (HTTPS). Použití protokolu HTTPS zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako je třeba prostředník, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Ujistěte se, že výchozí pravidlo přístupu k síti pro účty úložiště je nastavené na odepřít.

**ID**: CIS Azure 3,7 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Účty úložiště by měly omezovat síťový přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Přístup k síti účtů úložiště by měl být omezený. Nakonfigurujte Síťová pravidla, aby k účtu úložiště měly přístup jenom aplikace z povolených sítí. Aby bylo možné v připojeních z konkrétních internetových nebo místních klientů přistupovat k provozu z konkrétních virtuálních sítí Azure nebo do rozsahů veřejných IP adres sítě. |Audit, zamítnutí, zakázáno |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Zajistěte, aby se pro přístup k účtu úložiště povolily důvěryhodné služby Microsoftu.

**ID**: CIS Azure 3,8 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Účty úložiště by měly umožňovat přístup z důvěryhodných služeb Microsoftu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Některé služby Microsoftu, které pracují s účty úložiště, fungují ze sítí, které nemůžou udělit přístup prostřednictvím síťových pravidel. Aby mohl tento typ služby fungovat podle účelu, umožněte sadě důvěryhodných služeb společnosti Microsoft obejít Síťová pravidla. Tyto služby potom použijí silné ověřování pro přístup k účtu úložiště. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

## <a name="database-services"></a>Databázové služby

### <a name="ensure-that-auditing-is-set-to-on"></a>Zajistěte, aby bylo auditování nastaveno na zapnuto.

**ID**: CIS Azure 4,1 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Auditování na SQL serveru by mělo být povolené.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Auditování v SQL Server by mělo být povoleno ke sledování databázových činností napříč všemi databázemi na serveru, s výjimkou synapse a jejich uložení v protokolu auditu. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>Ujistěte se, že je správně nastavená zásada ' AuditActionGroups ' v zásadách auditování pro systém SQL Server.

**ID**: CIS Azure 4,2 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Nastavení auditování SQL by mělo mít Action-Groups nakonfigurovanou pro zachycení kritických aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ff426e2-515f-405a-91c8-4f2333442eb5) |Vlastnost AuditActionsAndGroups by měla obsahovat alespoň SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP, FAILED_DATABASE_AUTHENTICATION_GROUP BATCH_COMPLETED_GROUP, aby se zajistilo důkladné protokolování auditu. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_ActionsAndGroups_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Zajistěte, aby uchování auditování bylo větší než 90 dní.

**ID**: CIS Azure 4,3 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[SQL servery by měly být nakonfigurované s uchováním dat po dobu delší než 90 dní.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Auditovat SQL servery nakonfigurované s dobou uchování auditování kratší než 90 dní. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>Zajistěte, aby bylo rozšířené zabezpečení dat na SQL serveru nastavené na zapnuto.

**ID**: CIS Azure 4,4 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Auditujte všechny spravované instance SQL bez pokročilých zabezpečení dat. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Auditovat SQL servery bez pokročilých zabezpečení dat |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Ujistěte se, že je nakonfigurovaný správce Azure Active Directory.

**ID**: CIS Azure 4,8 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro SQL servery by se měl zřídit správce Azure Active Directory.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Proveďte audit zřizování správce Azure Active Directory pro SQL Server, aby se povolilo ověřování Azure AD. Ověřování Azure AD umožňuje zjednodušenou správu oprávnění a centralizovanou správu identit uživatelů databáze a dalších služeb Microsoftu. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Zajistěte, aby bylo šifrování dat u SQL Database nastaveno na zapnuto.

**ID**: CIS Azure 4,9 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Je třeba povolit transparentní šifrování dat databází SQL.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Transparentní šifrování dat by mělo být povoleno pro ochranu neaktivních dat a splnění požadavků na dodržování předpisů. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>Zajistěte, aby ochrana TDE systému SQL Server byla zašifrovaná pomocí BYOK (použijte vlastní klíč).

**ID**: CIS Azure 4,10 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Ochrana TDE spravované instance SQL by se měla šifrovat pomocí vlastního klíče.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Transparentní šifrování dat (TDE) s vaší vlastní klíčovou podporou přináší větší transparentnost a kontrolu nad ochranným prostředím TDE, zvýšené zabezpečení pomocí externí služby založené na HSM a povýšení oddělení povinností. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Ochrana TDE systému SQL Server by měla být zašifrovaná pomocí vlastního klíče](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Transparentní šifrování dat (TDE) s vaší vlastní klíčovou podporou přináší větší transparentnost a kontrolu nad ochranným prostředím TDE, zvýšené zabezpečení pomocí externí služby založené na HSM a povýšení oddělení povinností. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Zajistěte, aby se vynutilo připojení SSL u databázového serveru MySQL nastavené na povoleno.

**ID**: CIS Azure 4,11 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL podporuje připojení Azure Database for MySQLho serveru k klientským aplikacím pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž" v prostředních prostředcích, a to šifrováním datového proudu mezi serverem a vaší aplikací. Tato konfigurace vynutila, že protokol SSL má vždycky povolený přístup k databázovému serveru. |Audit, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Zajistěte, aby parametr serveru ' log_checkpoints ' byl pro databázový server PostgreSQL nastaven na hodnotu ' ON '.

**ID**: CIS Azure 4,12 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro databázové servery PostgreSQL by měly být povoleny kontrolní body protokolu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Tato zásada pomáhá auditovat všechny databáze PostgreSQL ve vašem prostředí, aniž by bylo povolené nastavení log_checkpoints. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Zajistěte, aby se vynutilo připojení SSL u databázového serveru PostgreSQL nastavené na povoleno.

**ID**: CIS Azure 4,13 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro databázové servery PostgreSQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL podporuje připojení Azure Database for PostgreSQLho serveru k klientským aplikacím pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž" v prostředních prostředcích, a to šifrováním datového proudu mezi serverem a vaší aplikací. Tato konfigurace vynutila, že protokol SSL má vždycky povolený přístup k databázovému serveru. |Audit, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Zajistěte, aby parametr serveru ' log_connections ' byl pro databázový server PostgreSQL nastaven na hodnotu ' ON '.

**ID**: CIS Azure 4,14 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro databázové servery PostgreSQL by se měla povolit protokolovat připojení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Tato zásada pomáhá auditovat všechny databáze PostgreSQL ve vašem prostředí, aniž by bylo povolené nastavení log_connections. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Zajistěte, aby parametr serveru ' log_disconnections ' byl pro databázový server PostgreSQL nastaven na hodnotu ' ON '.

**ID**: CIS Azure 4,15 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Odpojení by se měla protokolovat pro databázové servery PostgreSQL.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Tato zásada pomáhá auditovat všechny databáze PostgreSQL ve vašem prostředí, aniž by bylo povoleno log_disconnections. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Zajistěte, aby parametr serveru ' connection_throttling ' byl pro databázový server PostgreSQL nastaven na hodnotu ' ON '.

**ID**: CIS Azure 4,17 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro databázové servery PostgreSQL by mělo být povoleno omezování připojení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Tato zásada pomáhá auditovat všechny databáze PostgreSQL ve vašem prostředí bez povoleného omezení připojení. Toto nastavení umožňuje dočasné omezení počtu připojení na IP adresu pro příliš mnoho neplatných neúspěšných přihlášení k heslům. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

### <a name="ensure-that-a-log-profile-exists"></a>Zajistěte, aby existoval profil protokolu

**ID**: SNS – **vlastnictví**Azure 5.1.1: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Předplatná Azure by měla mít profil protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7796937f-307b-4598-941c-67d3a05ebfe7) |Tato zásada zajišťuje, jestli je profil protokolu povolený pro export protokolů aktivit. Audituje, jestli není vytvořený žádný profil protokolu pro export protokolů buď do účtu úložiště, nebo do centra událostí. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Logprofile_activityLogs_Audit.json) |

### <a name="ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>Zajistěte, aby uchování protokolu aktivit bylo nastavené na 365 dní nebo víc.

**ID**: SNS – **vlastnictví**Azure 5.1.2: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Protokol aktivit by se měl uchovávat aspoň po dobu jednoho roku.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb02aacc0-b073-424e-8298-42b22829ee0a) |Tato zásada Audituje protokol aktivit, pokud doba uchování není nastavená na 365 dní nebo trvale (dny uchování nastavené na 0). |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLogRetention_365orGreater.json) |

### <a name="ensure-audit-profile-captures-all-the-activities"></a>Ujistěte se, že profil auditu zachycuje všechny aktivity.

**ID**: CIS **vlastnictví**Azure 5.1.3: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Profil protokolu Azure Monitor by měl shromažďovat protokoly pro kategorie Write, DELETE a Action.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Tato zásada zajišťuje, že profil protokolu shromažďuje protokoly pro kategorie Write, DELETE a Action. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>Ujistěte se, že profil protokolu zachycuje protokoly aktivit pro všechny oblasti, včetně globálních

**ID**: SNS – **vlastnictví**Azure 5.1.4: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Azure Monitor by měly shromažďovat protokoly aktivit ze všech oblastí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Tato zásada Audituje profil Azure Monitorho protokolu, který neexportuje aktivity ze všech podporovaných oblastí Azure, včetně globálního. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Zajistěte, aby byl účet úložiště obsahující kontejner s protokoly aktivit zašifrovaný pomocí BYOK (použijte vlastní klíč).

**ID**: CIS Azure 5.1.6 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Účet úložiště obsahující kontejner s protokoly aktivit musí být zašifrovaný pomocí BYOK.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Tato zásada Audituje, jestli je účet úložiště obsahující kontejner s protokoly aktivit zašifrovaný pomocí BYOK. Zásada funguje jenom v případě, že účet úložiště se nachází ve stejném předplatném jako protokoly aktivit podle návrhu. Další informace o šifrování Azure Storage v klidovém umístění najdete tady [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok) .  |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Ujistěte se, že protokolování služby Azure webrecovery je povolené.

**ID**: CIS Azure 5.1.7 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Měly by být povolené diagnostické protokoly v Key Vault.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Audituje povolení diagnostických protokolů. Díky tomu můžete znovu vytvořit stopy aktivity, které se použijí pro účely šetření, když dojde k bezpečnostnímu incidentu nebo když dojde k ohrožení zabezpečení sítě. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Ujistěte se, že existuje výstraha protokolu aktivit pro vytvoření přiřazení zásady.

**ID**: SNS – **vlastnictví**Azure 5.2.1: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace zásad by mělo existovat upozornění protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Tato zásada Audituje konkrétní operace zásad bez nakonfigurovaného nastavení výstrahy protokolu aktivit. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Zajistěte, aby pro vytvoření nebo aktualizaci skupiny zabezpečení sítě existovalo upozornění protokolu aktivit.

**ID**: SNS – **vlastnictví**Azure 5.2.2: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace správy by měla existovat výstraha protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Tato zásada Audituje konkrétní operace správy bez nakonfigurovaného nastavení výstrahy protokolu aktivit. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Zajistěte, aby pro odstranění skupiny zabezpečení sítě existovalo upozornění protokolu aktivit.

**ID**: SNS – **vlastnictví**Azure 5.2.3: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace správy by měla existovat výstraha protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Tato zásada Audituje konkrétní operace správy bez nakonfigurovaného nastavení výstrahy protokolu aktivit. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Zajistěte, aby pro pravidlo vytvoření nebo aktualizace skupiny zabezpečení sítě existovalo upozornění protokolu aktivit.

**ID**: SNS – **vlastnictví**Azure 5.2.4: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace správy by měla existovat výstraha protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Tato zásada Audituje konkrétní operace správy bez nakonfigurovaného nastavení výstrahy protokolu aktivit. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Zajistěte, aby pro odstranění pravidla skupiny zabezpečení sítě existovalo upozornění protokolu aktivit.

**ID**: CIS Azure 5.2.5 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace správy by měla existovat výstraha protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Tato zásada Audituje konkrétní operace správy bez nakonfigurovaného nastavení výstrahy protokolu aktivit. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Zajistěte, aby pro vytvoření nebo aktualizaci řešení zabezpečení existovalo upozornění protokolu aktivit.

**ID**: CIS Azure 5.2.6 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace zabezpečení by mělo existovat upozornění protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Tato zásada Audituje konkrétní operace zabezpečení bez nakonfigurovaného upozornění protokolu aktivit. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Zajistěte, aby pro odstranění řešení zabezpečení existovalo upozornění protokolu aktivit.

**ID**: CIS Azure 5.2.7 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace zabezpečení by mělo existovat upozornění protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Tato zásada Audituje konkrétní operace zabezpečení bez nakonfigurovaného upozornění protokolu aktivit. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Zajistěte, aby pro pravidlo brány firewall pro vytvoření nebo aktualizaci nebo odstranění SQL Server existovalo upozornění protokolu aktivit.

**ID**: CIS Azure 5.2.8 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace správy by měla existovat výstraha protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Tato zásada Audituje konkrétní operace správy bez nakonfigurovaného nastavení výstrahy protokolu aktivit. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-update-security-policy"></a>Ujistěte se, že pro zásady zabezpečení aktualizace existují výstrahy protokolu aktivit.

**ID**: CIS Azure 5.2.9 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace zabezpečení by mělo existovat upozornění protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Tato zásada Audituje konkrétní operace zabezpečení bez nakonfigurovaného upozornění protokolu aktivit. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

## <a name="networking"></a>Sítě

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>Zajistěte, aby byl přístup protokolu RDP omezen z Internetu.

**ID**: CIS Azure 6,1 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Přístup RDP z Internetu by měl být blokovaný.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Tato zásada Audituje jakékoli pravidlo zabezpečení sítě, které umožňuje přístup k protokolu RDP z Internetu. |Audit, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>Zajistěte, aby přístup přes protokol SSH byl omezen z Internetu.

**ID**: CIS Azure 6,2 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Přístup přes SSH z Internetu by měl být zablokovaný.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Tato zásada Audituje jakékoli pravidlo zabezpečení sítě, které umožňuje přístup přes SSH z Internetu. |Audit, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Ujistěte se, že Network Watcher je povolené.

**ID**: CIS Azure 6,5 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Network Watcher by měl být povolený.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher je místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni síťového scénáře v, do a z Azure. Monitorování úrovně scénáře umožňuje diagnostikovat problémy v zobrazení na úrovni koncových sítí. Nástroje pro diagnostiku a diagnostiku sítě, které jsou dostupné v Network Watcher, vám pomůžou pochopit, diagnostikovat a získávat přehledy vaší sítě v Azure. |auditIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Virtuální počítače

### <a name="ensure-that-os-disk-are-encrypted"></a>Ujistěte se, že je disk s operačním systémem zašifrovaný.

**ID**: CIS Azure 7,1 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Na virtuálních počítačích by se mělo použít šifrování disku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Virtuální počítače bez povoleného šifrování disku se monitorují Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-data-disks-are-encrypted"></a>Zajistěte, aby byly datové disky šifrované.

**ID**: CIS Azure 7,2 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Na virtuálních počítačích by se mělo použít šifrování disku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Virtuální počítače bez povoleného šifrování disku se monitorují Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted"></a>Zajistěte, aby byly nepřipojené disky šifrované.

**ID**: CIS Azure 7,3 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Nepřipojené disky by měly být zašifrované](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Tato zásada Audituje všechny nepřipojené disky bez povoleného šifrování. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Zajistěte, aby byla nainstalovaná jenom schválená rozšíření.

**ID**: CIS Azure 7,4 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Musí být nainstalovaná jenom schválená rozšíření virtuálních počítačů.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Tato zásada řídí rozšíření virtuálních počítačů, která nejsou schválena. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Zajistěte, aby se používaly nejnovější opravy operačního systému pro všechny Virtual Machines.

**ID**: CIS Azure 7,5 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Na počítače by se měly nainstalovat aktualizace systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Chybějící aktualizace systému zabezpečení na vašich serverech se monitorují Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Ujistěte se, že je nainstalovaná ochrana koncového bodu pro všechny Virtual Machines.

**ID**: CIS Azure 7,6 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Monitorovat chybějící Endpoint Protection v Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servery bez nainstalovaného agenta Endpoint Protection se budou monitorovat Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Další informace o zabezpečení

### <a name="ensure-the-key-vault-is-recoverable"></a>Ujistěte se, že je Trezor klíčů obnovitelný.

**ID**: CIS Azure 8,4 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Key Vault objekty by měly být obnovitelné](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Tato zásada Audituje, jestli nejsou objekty trezoru klíčů obnovitelné. Funkce obnovitelného odstranění pomáhá efektivně uchovávat prostředky pro danou dobu uchování (90 dní) i po operaci odstranění a zároveň dává dojem, že se objekt odstranil. Pokud je zapnutá možnost vyprázdnění ochrany, trezor nebo objekt v odstraněném stavu nelze odstranit, dokud neuplyne doba uchování 90 dnů. Tyto trezory a objekty můžou být pořád obnoveny, takže zákazníci, kteří budou dodržovat zásady uchovávání informací. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Povolení řízení přístupu na základě role (RBAC) v rámci služeb Azure Kubernetes Services

**ID**: CIS Azure 8,5 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro služby Kubernetes by se měla použít Access Control na základě rolí (RBAC).](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |K zajištění podrobného filtrování akcí, které mohou uživatelé provádět, použijte Role-Based Access Control (RBAC) ke správě oprávnění v clusterech služby Kubernetes a ke konfiguraci relevantních zásad autorizace. |Audit, zakázáno |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="appservice"></a>AppService

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Ujistěte se, že je App Service ověřování nastaveno na Azure App Service

**ID**: CIS Azure 9,1 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V aplikaci API by mělo být povolené ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |Ověřování Azure App Service je funkce, která může zabránit tomu, aby anonymní požadavky HTTP dostaly do aplikace API nebo aby ověřovala ty, které mají tokeny před dosažením aplikace API. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[Ve vaší aplikaci Function app by mělo být povolené ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |Ověřování Azure App Service je funkce, která může zabránit tomu, aby anonymní požadavky HTTP dostaly do aplikace Function App nebo ověřovala ty, které mají tokeny předtím, než dosáhnou aplikace Function App. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[Ve vaší webové aplikaci by mělo být povolené ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |Ověřování Azure App Service je funkce, která může zabránit tomu, aby se anonymní požadavky HTTP dostaly do webové aplikace, nebo aby se ověřily, které mají tokeny před dosažením webové aplikace. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Ujistěte se, že webová aplikace přesměruje veškerý provoz HTTP na HTTPS v Azure App Service

**ID**: CIS Azure 9,2 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Webová aplikace by měla být přístupná jen přes protokol HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Zajistěte, aby webová aplikace používala nejnovější verzi šifrování TLS.

**ID**: CIS Azure 9,3 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V aplikaci API by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[V Function App by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Ve vaší webové aplikaci by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Ujistěte se, že má webová aplikace nastavené klientské certifikáty (příchozí klientské certifikáty) na zapnuto.

**ID**: CIS Azure 9,4 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Zajistěte, aby měla aplikace API pro klientské certifikáty (příchozí klientské certifikáty) nastavené na zapnuto.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |Klientské certifikáty umožňují, aby aplikace požádala o certifikát pro příchozí požadavky. Aplikaci budou moci kontaktovat pouze klienti s platným certifikátem. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Zajistěte, aby aplikace Function App měla klientské certifikáty (příchozí klientské certifikáty) nastavené na zapnuto.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |Klientské certifikáty umožňují, aby aplikace požádala o certifikát pro příchozí požadavky. Aplikaci budou moci kontaktovat pouze klienti s platným certifikátem. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |
|[Zajistěte, aby webová aplikace měla klientské certifikáty (příchozí klientské certifikáty) nastavené na zapnuto.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Klientské certifikáty umožňují, aby aplikace požádala o certifikát pro příchozí požadavky. Aplikaci budou moci kontaktovat pouze klienti s platným certifikátem. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Ujistěte se, že je v App Service povolená možnost registrovat v Azure Active Directory.

**ID**: CIS Azure 9,5 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Ve vaší aplikaci API by se měla používat spravovaná identita.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Použití spravované identity pro rozšířené zabezpečení ověřování |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[Ve vašem Function App by se měla používat spravovaná identita.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Použití spravované identity pro rozšířené zabezpečení ověřování |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Ve vaší webové aplikaci by se měla použít spravovaná identita.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Použití spravované identity pro rozšířené zabezpečení ověřování |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Zajistěte, aby byla verze PHP nejnovější, pokud se používá ke spuštění webové aplikace.

**ID**: CIS Azure 9,7 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Zajistěte, aby byla verze PHP nejnovější, pokud se používá jako součást aplikace API.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |V důsledku chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze softwaru PHP. Použití nejnovější verze PHP pro aplikace API se doporučuje, aby bylo možné využívat opravy zabezpečení, pokud jsou nějaké nebo nové funkce nejnovější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Zajistěte, aby byla verze PHP nejnovější, pokud se používá jako součást webové aplikace.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |V důsledku chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze softwaru PHP. Použití nejnovější verze PHP pro webové aplikace se doporučuje, aby bylo možné využívat opravy zabezpečení, pokud jsou nějaké nebo nové funkce nejnovější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Zajistěte, aby byla nejnovější verze Pythonu, pokud se používá ke spuštění webové aplikace.

**ID**: CIS Azure 9,8 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Zajistěte, aby byla nejnovější verze Pythonu, pokud se používá jako součást aplikace API.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Z důvodu chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze softwaru Pythonu. Použití nejnovější verze Pythonu pro aplikace API se doporučuje, aby bylo možné využívat opravy zabezpečení, pokud jsou nějaké nebo nové funkce nejnovější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Zajistěte, aby byla nejnovější verze Pythonu, pokud se používá jako součást aplikace Function App.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Z důvodu chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze softwaru Pythonu. Použití nejnovější verze Pythonu pro aplikace Function App se doporučuje, aby bylo možné využívat opravy zabezpečení, pokud jsou nějaké nebo nové funkce nejnovější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Zajistěte, aby byla nejnovější verze Pythonu, pokud se používá jako součást webové aplikace.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Z důvodu chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze softwaru Pythonu. Použití nejnovější verze Pythonu pro Web Apps se doporučuje, aby bylo možné využívat opravy zabezpečení, pokud jsou nějaké nebo nové funkce nejnovější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Zajistěte, aby byl jazyk Java verze nejnovější, pokud se používá ke spuštění webové aplikace.

**ID**: CIS Azure 9,9 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Zajistěte, aby byl jazyk Java verze nejnovější, pokud se používá jako součást aplikace API.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Z důvodu chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze pro Java. Použití nejnovější verze Pythonu pro aplikace API se doporučuje, aby bylo možné využívat opravy zabezpečení, pokud jsou nějaké nebo nové funkce nejnovější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Zajistěte, aby byl jazyk Java verze nejnovější, pokud se používá jako součást aplikace Function App.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Z důvodu chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze pro software Java. Použití nejnovější verze Java pro aplikace Function App se doporučuje, aby bylo možné využívat opravy zabezpečení, pokud jsou nějaké nebo nové funkce nejnovější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Zajistěte, aby byl jazyk Java verze nejnovější, pokud se používá jako součást webové aplikace.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Z důvodu chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze pro software Java. Použití nejnovější verze Java pro webové aplikace se doporučuje, aby bylo možné využívat opravy zabezpečení, pokud jsou nějaké nebo nové funkce nejnovější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Ujistěte se, že hodnota HTTP verze je nejnovější, pokud se používá ke spuštění webové aplikace.

**ID**: CIS Azure 9,10 **vlastnictví**: zákazník

|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Ujistěte se, že hodnota HTTP verze je nejnovější, pokud se používá ke spuštění aplikace API.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |Z důvodu chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze pro protokol HTTP. Využijte nejnovější verzi protokolu HTTP pro webové aplikace, abyste mohli využít opravy zabezpečení, pokud jsou nějaké nebo nové funkce novější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Ujistěte se, že hodnota HTTP Version je nejnovější, pokud se používá ke spuštění aplikace Function App.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |Z důvodu chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze pro protokol HTTP. Využijte nejnovější verzi protokolu HTTP pro webové aplikace, abyste mohli využít opravy zabezpečení, pokud jsou nějaké nebo nové funkce novější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Ujistěte se, že hodnota HTTP verze je nejnovější, pokud se používá ke spuštění webové aplikace.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Z důvodu chyb zabezpečení nebo zahrnutí dalších funkcí jsou pravidelně vydávány novější verze pro protokol HTTP. Využijte nejnovější verzi protokolu HTTP pro webové aplikace, abyste mohli využít opravy zabezpečení, pokud jsou nějaké nebo nové funkce novější verze. V současné době platí tato zásada jenom pro Linux Web Apps. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

> [!NOTE]
> Dostupnost konkrétních definic Azure Policy se může v Azure Government a dalších národních cloudech lišit.

## <a name="next-steps"></a>Další kroky

Další články o Azure Policy:

- Přehled [dodržování legislativních předpisů](../concepts/regulatory-compliance.md) .
- Podívejte se na [strukturu definice iniciativy](../concepts/initiative-definition-structure.md).
- Podívejte se na další příklady na [Azure Policy Samples](./index.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
