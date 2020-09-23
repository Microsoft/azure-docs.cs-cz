---
title: Podrobnosti dodržování předpisů v souladu s předpisy pro HIPAA HITRUST 9,2
description: Podrobnosti o integrované iniciativě pro dodržování předpisů v HIPAA HITRUST 9,2 Každý ovládací prvek je namapován na jednu nebo více Azure Policy definic, které pomáhají s posouzením.
ms.date: 09/16/2020
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 84e346e436d09245262395450f74297931b2afe8
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90981750"
---
# <a name="details-of-the-hipaa-hitrust-92-regulatory-compliance-built-in-initiative"></a>Podrobnosti o integrované iniciativě dodržování předpisů v HIPAA HITRUST 9,2

Následující článek podrobně popisuje, jak Azure Policy integrovaná definice iniciativ dodržování legislativních předpisů mapuje na domény a **ovládací prvky** **dodržování předpisů** v HIPAA HiTRUST 9,2.
Další informace o tomto standardu dodržování předpisů najdete v tématu [HIPAA HITRUST 9,2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html). Pokud chcete pochopit _vlastnictví_, přečtěte si téma [definice zásad Azure Policy](../concepts/definition-structure.md#type) a [sdílená odpovědnost v cloudu](../../../security/fundamentals/shared-responsibility.md).

Následující mapování jsou ovládací prvky **HIPAA HITRUST 9,2** . Pomocí navigace na pravé straně můžete přejít přímo ke konkrétní **doméně dodržování předpisů**. Mnohé z ovládacích prvků jsou implementovány s definicí [Azure Policy](../overview.md) iniciativou. Chcete-li zkontrolovat definici kompletní iniciativy, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** .
Pak vyhledejte a vyberte definici integrované iniciativy **HiTRUST/HIPAA** pro legislativní dodržování předpisů.

Tato integrovaná iniciativa se nasadí jako součást ukázky podrobného plánu [HIPAA HITRUST 9,2](../../blueprints/samples/hipaa-hitrust-9-2.md).

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../overview.md) .
> Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../how-to/get-compliance-data.md) pomocí ovládacího prvku. často však není 1:1 nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **se v Azure Policy** vztahuje pouze na samotné definice zásad; Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard kompatibility zahrnuje i ovládací prvky, které nejsou v tuto chvíli řešeny žádnými definicemi Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi doménami dodržování předpisů, ovládacími prvky a definicemi Azure Policy pro tento standard dodržování předpisů se mohou v průběhu času měnit. Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/HIPAA_HITRUST_audit.json).

## <a name="privilege-management"></a>Správa oprávnění

### <a name="access-to-management-functions-or-administrative-consoles-for-systems-hosting-virtualized-systems-are-restricted-to-personnel-based-upon-the-principle-of-least-privilege-and-supported-through-technical-controls"></a>Přístup ke správcovským funkcím a konzolám pro správu pro systémy hostující virtualizované systémy jsou omezené na pracovníky založené na principu minimálního oprávnění a podporovaných prostřednictvím technických ovládacích prvků.

**ID**: 11180.01 c3system. 6 – 01. **vlastnictví**v jazyce c: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Porty pro správu virtuálních počítačů by měly být chráněné pomocí řízení přístupu k síti za běhu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Přístup k síti je možný jenom v čase (JIT) se monitoruje Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="privileges-are-formally-authorized-and-controlled-allocated-to-users-on-a-need-to-use-and-event-by-event-basis-for-their-functional-role-eg-user-or-administrator-and-documented-for-each-system-productelement"></a>Oprávnění jsou formálně autorizováná a řízená, která se přidělují uživatelům na základě jejich funkční role (třeba uživatele nebo správce) a jsou zdokumentovány pro každý systémový produkt nebo prvek.

**ID**: 1143.01 c1system. 123-01. **vlastnictví**v jazyce c: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Porty pro správu by měly být uzavřeny na virtuálních počítačích](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Otevřené porty pro vzdálenou správu zveřejňují váš virtuální počítač na vysokou úroveň rizika vyplývající z internetových útoků. Tyto útoky se pokoušejí získat přístup správce k počítači prostřednictvím přihlašovacích údajů hrubou silou. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-explicitly-authorizes-access-to-specific-security-relevant-functions-deployed-in-hardware-software-and-firmware-and-security-relevant-information"></a>Organizace explicitně autorizuje přístup ke konkrétním souvisejícím funkcím zabezpečení (nasazeným v hardwaru, softwaru a firmwaru) a informacím týkajícím se zabezpečení.

**ID**: 1144.01 c1system. 4-01. **vlastnictví**v jazyce c: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Doporučuje se určit až 3 vlastníky předplatného, aby se snížila pravděpodobnost porušení napadeného vlastníka. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="role-based-access-control-is-implemented-and-capable-of-mapping-each-user-to-one-or-more-roles-and-each-role-to-one-or-more-system-functions"></a>Řízení přístupu na základě role je implementované a umožňuje mapování jednotlivých uživatelů na jednu nebo více rolí a každou roli na jednu nebo více systémových funkcí.

**ID**: 1145.01 c2system. 1-01. **vlastnictví**v jazyce c: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Doporučuje se určit více než jednoho vlastníka předplatného, aby měl přístup správce k redundanci. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="the-organization-promotes-the-development-and-use-of-programs-that-avoid-the-need-to-run-with-elevated-privileges-and-system-routines-to-avoid-the-need-to-grant-privileges-to-users"></a>Organizace podporuje vývoj a používání programů, které zabraňují nutnosti spouštět se zvýšenými oprávněními a systémovými rutinami, aby se zabránilo nutnosti udělovat oprávnění uživatelům.

**ID**: 1146.01 c2system. 23 – 01. **vlastnictví**v jazyce c: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Aby nedocházelo k nemonitorovanému přístupu, měly by se z předplatného odebrat externí účty s oprávněním vlastníka. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |

### <a name="elevated-privileges-are-assigned-to-a-different-user-id-from-those-used-for-normal-business-use-all-users-access-privileged-services-in-a-single-role-and-such-privileged-access-is-minimized"></a>Zvýšená oprávnění se přiřazují jinému ID uživatele z těch, které se používají pro normální obchodní použití, všichni uživatelé přistupují k privilegovaným službám v jediné roli a takový privilegovaný přístup se minimalizuje.

**ID**: 1147.01 c2system. 456-01. **vlastnictví**v jazyce c: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |Zastaralé účty s oprávněním vlastníka by měly být odebrány z vašeho předplatného.  Zastaralé účty jsou účty, kterým se zablokuje přihlášení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |

### <a name="the-organization-restricts-access-to-privileged-functions-and-all-security-relevant-information"></a>Organizace omezuje přístup k privilegovaným funkcím a všem informacím souvisejícím se zabezpečením.

**ID**: 1148.01 c2system. 78-01. **vlastnictví**c: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Auditovat využití vlastních pravidel RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Místo vlastních rolí RBAC, které jsou náchylné k chybám, byste měli auditovat předdefinované role, jako je vlastník, přispěvatel, čtenář. Použití vlastních rolí se považuje za výjimku a vyžaduje přísné kontroly a modelování hrozeb. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Počítače s Windows by měly splňovat požadavky na možnosti zabezpečení účty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee984370-154a-4ee8-9726-19d900e56fc0) |Počítače se systémem Windows by měly mít zadaná nastavení Zásady skupiny v kategorii možnosti zabezpečení-účty pro omezení používání prázdných hesel a stavu účtu Guest pro místní účet. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAccounts_AINE.json) |

### <a name="the-organization-facilitates-information-sharing-by-enabling-authorized-users-to-determine-a-business-partners-access-when-discretion-is-allowed-as-defined-by-the-organization-and-by-employing-manual-processes-or-automated-mechanisms-to-assist-users-in-making-information-sharingcollaboration-decisions"></a>Tato organizace usnadňuje sdílení informací tím, že umožňuje autorizovaným uživatelům určit přístup obchodních partnerů, pokud je podle potřeby vymezená organizace, a díky tomu, že využívají ruční procesy nebo automatizované mechanismy, aby uživatelům usnadnili rozhodování o sdílení a spolupráci.

**ID**: 1149.01 c2system. 9-01. **vlastnictví**c: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro služby Kubernetes by se měla použít Access Control na základě rolí (RBAC).](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |K zajištění podrobného filtrování akcí, které mohou uživatelé provádět, použijte Access Control na základě rolí (RBAC) ke správě oprávnění v clusterech služby Kubernetes a ke konfiguraci relevantních zásad autorizace. |Audit, zakázáno |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="the-access-control-system-for-the-system-components-storing-processing-or-transmitting-covered-information-is-set-with-a-default-quotdeny-allquot-setting"></a>Systém řízení přístupu pro součásti systému, který ukládá, zpracovává nebo odesílá zahrnuté informace, je nastaven s výchozím &quot; nastavením Odepřít – vše &quot; .

**ID**: 1150.01 c2system. 10-01. **vlastnictví**c: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Porty pro správu by měly být uzavřeny na virtuálních počítačích](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Otevřené porty pro vzdálenou správu zveřejňují váš virtuální počítač na vysokou úroveň rizika vyplývající z internetových útoků. Tyto útoky se pokoušejí získat přístup správce k počítači prostřednictvím přihlašovacích údajů hrubou silou. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-limits-authorization-to-privileged-accounts-on-information-systems-to-a-pre-defined-subset-of-users"></a>Organizace omezuje autorizaci privilegovaných účtů v informačních systémech na předem definovanou podmnožinu uživatelů.

**ID**: 1151.01 c3system. 1-01. **vlastnictví**v jazyce c: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Doporučuje se určit až 3 vlastníky předplatného, aby se snížila pravděpodobnost porušení napadeného vlastníka. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-audits-the-execution-of-privileged-functions-on-information-systems-and-ensures-information-systems-prevent-non-privileged-users-from-executing-privileged-functions"></a>Organizace Audituje provádění privilegovaných funkcí v informačních systémech a zajišťuje, aby informační systémy bránila neprivilegovaným uživatelům ve spouštění privilegovaných funkcí.

**ID**: 1152.01 c3system. 2-01. **vlastnictví**v jazyce c: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Doporučuje se určit více než jednoho vlastníka předplatného, aby měl přístup správce k redundanci. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="all-file-system-access-not-explicitly-required-is-disabled-and-only-authorized-users-are-permitted-access-to-only-that-which-is-expressly-required-for-the-performance-of-the-users-job-duties"></a>Veškerý přístup k systému souborů není explicitně vyžadován a přístup pouze autorizovaným uživatelům je povolen pouze k tomu, který je výslovně vyžadován pro výkon pracovních povinností uživatelů.

**ID**: 1153.01 c3system. 35-01. **vlastnictví**v jazyce c: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro služby Kubernetes by se měla použít Access Control na základě rolí (RBAC).](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |K zajištění podrobného filtrování akcí, které mohou uživatelé provádět, použijte Access Control na základě rolí (RBAC) ke správě oprávnění v clusterech služby Kubernetes a ke konfiguraci relevantních zásad autorizace. |Audit, zakázáno |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="contractors-are-provided-with-minimal-system-and-physical-access-only-after-the-organization-assesses-the-contractors-ability-to-comply-with-its-security-requirements-and-the-contractor-agrees-to-comply"></a>Smluvním partnerům se poskytuje minimální systémový a fyzický přístup, jenom když organizace posuzuje schopnost zhotovitele dodržovat požadavky na zabezpečení a zhotovitel souhlasí s tím, že bude dodržovat předpisy.

**ID**: 1154.01 c3system. 4-01. **vlastnictví**v jazyce c: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Doporučuje se určit až 3 vlastníky předplatného, aby se snížila pravděpodobnost porušení napadeného vlastníka. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

## <a name="user-authentication-for-external-connections"></a>Ověřování uživatelů pro externí připojení

### <a name="strong-authentication-methods-such-as-multi-factor-radius-or-kerberos-for-privileged-access-and-chap-for-encryption-of-credentials-for-dialup-methods-are-implemented-for-all-external-connections-to-the-organizations-network"></a>Metody silného ověřování, jako je Multi-Factor, RADIUS nebo Kerberos (pro privilegovaný přístup) a CHAP (pro šifrování přihlašovacích údajů pro metody telefonického připojení), jsou implementované pro všechna externí připojení k síti organizací.

**ID**: 1116.01 j1organizational. 145-01. j **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) by mělo být povolené pro všechny účty předplatného s oprávněním vlastníka, aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="remote-access-by-vendors-and-business-partners-eg-for-remote-maintenance-is-disableddeactivated-when-not-in-use"></a>Vzdálený přístup dodavatelům a obchodním partnerům (například pro vzdálenou údržbu) je zakázán nebo deaktivován, pokud se nepoužívá.

**ID**: 1117.01 j1organizational. 23 – 01. j **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V rámci vašeho předplatného by měly být povolené účty s oprávněním k zápisu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) by mělo být povolené pro všechny účty předplatného s oprávněním k zápisu, aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="organizations-implement-encryption-eg-nbspvpn-solutions-or-private-lines-and-logs-remote-access-to-the-organizations-network-by-employees-contractors-or-third-party-eg-vendors"></a>Organizace implementují šifrování (například &nbsp; řešení VPN nebo soukromé linky) a protokolují vzdálený přístup k podnikové síti zaměstnanci, smluvním partnerům nebo třetím stranám (například dodavatelům).

**ID**: 1118.01 j2organizational. 124 – 01. j **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |U všech účtů předplatného s oprávněním ke čtení by se mělo povolit Multi-Factor Authentication (MFA), aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="network-equipment-is-checked-for-unanticipated-dial-up-capabilities"></a>Síťové zařízení je kontrolováno na neočekávané možnosti telefonického připojení.

**ID**: 1119.01 j2organizational. 3-01. **vlastnictví**j: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Porty pro správu virtuálních počítačů by měly být chráněné pomocí řízení přístupu k síti za běhu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Přístup k síti je možný jenom v čase (JIT) se monitoruje Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="remote-administration-sessions-are-authorized-encrypted-and-employ-increased-security-measures"></a>Relace vzdálené správy jsou autorizovány, šifrovány a využívají zvýšené bezpečnostní míry.

**ID**: 1121.01 j3organizational. 2 – 01. j **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) by mělo být povolené pro všechny účty předplatného s oprávněním vlastníka, aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="if-encryption-is-not-used-for-dial-up-connections-the-cio-or-hisher-designated-representative-provides-specific-written-authorization"></a>Pokud se šifrování nepoužívá pro Vytáčená připojení, CIO nebo jeho pověřený zástupce poskytuje konkrétní písemnou autorizaci.

**ID**: 1173.01 j1organizational. 6 – 01. **vlastnictví**j: nelze použít

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V rámci vašeho předplatného by měly být povolené účty s oprávněním k zápisu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) by mělo být povolené pro všechny účty předplatného s oprávněním k zápisu, aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="the-organization-protects-wireless-access-to-systems-containing-sensitive-information-by-authenticating-both-users-and-devices"></a>Organizace chrání bezdrátový přístup k systémům obsahujícím citlivé údaje tím, že ověřuje uživatele i zařízení.

**ID**: 1174.01 j1organizational. 7 – 01. **vlastnictví**j: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |U všech účtů předplatného s oprávněním ke čtení by se mělo povolit Multi-Factor Authentication (MFA), aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="remote-access-to-business-information-across-public-networks-only-takes-place-after-successful-identification-and-authentication"></a>Vzdálený přístup k obchodním informacím přes veřejné sítě se provádí jenom po úspěšné identifikaci a ověřování.

**ID**: 1175.01 j1organizational. 8 – 01. j **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Porty pro správu virtuálních počítačů by měly být chráněné pomocí řízení přístupu k síti za běhu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Přístup k síti je možný jenom v čase (JIT) se monitoruje Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="the-organization-requires-a-callback-capability-with-re-authentication-to-verify-dial-up-connections-from-authorized-locations"></a>Aby bylo možné ověřit Vytáčená připojení z autorizovaných umístění, společnost vyžaduje funkci zpětného volání s opakovaným ověřením.

**ID**: 1176.01 j2organizational. 5 – 01. j **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) by mělo být povolené pro všechny účty předplatného s oprávněním vlastníka, aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="user-ids-assigned-to-vendors-are-reviewed-in-accordance-with-the-organizations-access-review-policy-at-a-minimum-annually"></a>ID uživatelů přiřazená dodavatelům se v souladu se zásadami kontroly přístupu organizace přezkoumávají minimálně jednou ročně.

**ID**: 1177.01 j2organizational. 6 – 01. j **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V rámci vašeho předplatného by měly být povolené účty s oprávněním k zápisu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) by mělo být povolené pro všechny účty předplatného s oprávněním k zápisu, aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="node-authentication-including-cryptographic-techniques-eg-machine-certificates-serves-as-an-alternative-means-of-authenticating-groups-of-remote-users-where-they-are-connected-to-a-secure-shared-computer-facility"></a>Ověřování uzlů, včetně kryptografických technik (například počítačových certifikátů), slouží jako alternativní způsob ověřování skupin vzdálených uživatelů v případě, že jsou připojeni k zabezpečenému a sdílenému počítačovému zařízení.

**ID**: 1178.01 j2organizational. 7 – 01. j **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |U všech účtů předplatného s oprávněním ke čtení by se mělo povolit Multi-Factor Authentication (MFA), aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-monitors-and-controls-remote-access-methods"></a>Informační systém sleduje a ovládá metody vzdáleného přístupu.

**ID**: 1179.01 j3organizational. 1-01. j **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Porty pro správu virtuálních počítačů by měly být chráněné pomocí řízení přístupu k síti za běhu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Přístup k síti je možný jenom v čase (JIT) se monitoruje Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Ochrana portů pro vzdálenou diagnostiku a konfiguraci

### <a name="access-to-network-equipment-is-physically-protected"></a>Přístup k síťovému vybavení je fyzicky chráněný.

**ID**: 1192.01 l1organizational. 1-01. l **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Porty pro správu virtuálních počítačů by měly být chráněné pomocí řízení přístupu k síti za běhu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Přístup k síti je možný jenom v čase (JIT) se monitoruje Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="controls-for-the-access-to-diagnostic-and-configuration-ports-include-the-use-of-a-key-lock-and-the-implementation-of-supporting-procedures-to-control-physical-access-to-the-port"></a>Ovládací prvky pro přístup k portům pro diagnostiku a konfiguraci zahrnují použití zámku klíče a implementaci podpůrných postupů pro řízení fyzického přístupu k portu.

**ID**: 1193.01 l2organizational. 13-01. l **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Porty pro správu by měly být uzavřeny na virtuálních počítačích](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Otevřené porty pro vzdálenou správu zveřejňují váš virtuální počítač na vysokou úroveň rizika vyplývající z internetových útoků. Tyto útoky se pokoušejí získat přístup správce k počítači prostřednictvím přihlašovacích údajů hrubou silou. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="ports-services-and-similar-applications-installed-on-a-computer-or-network-systems-which-are-not-specifically-required-for-business-functionality-are-disabled-or-removed"></a>Porty, služby a podobné aplikace nainstalované v počítači nebo v síťových systémech, které nejsou konkrétně vyžadované pro obchodní funkce, jsou zakázané nebo odebrané.

**ID**: 1194.01 l2organizational. 2-01. l **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Vzdálené ladění by mělo být pro webové aplikace vypnuté.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |Vzdálené ladění vyžaduje, aby byly na webové aplikaci otevřené porty pro příchozí spojení. Vzdálené ladění by mělo být vypnuté. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="the-organization-reviews-the-information-system-within-every-three-hundred-and-sixty--five-365-days-to-identify-and-disables-unnecessary-and-non-secure-functions-ports-protocols-andor-services"></a>Organizace zkontroluje informační systém během každých 365 (365) dnů a určí a zakáže nepotřebné a nezabezpečené funkce, porty, protokoly a služby.

**ID**: 1195.01 l3organizational. 1-01. l **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro aplikace Function app by mělo být vypnuto vzdálené ladění.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Vzdálené ladění vyžaduje, aby byly pro aplikace Function App otevřené porty pro příchozí spojení. Vzdálené ladění by mělo být vypnuté. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-identifies-unauthorized-blacklisted-software-on-the-information-system-prevents-program-execution-in-accordance-with-a-list-of-unauthorized-blacklisted-software-programs-employs-an-allow-all-deny-by-exception-policy-to-prohibit-execution-of-known-unauthorized-blacklisted-software-and-reviews-and-updates-the-list-of-unauthorized-blacklisted-software-programs-annually"></a>Organizace identifikuje neoprávněný (zakázaný) software v informačním systému, zabrání spuštění programu v souladu se seznamem neautorizovaných (zakázaných) softwarových programů, který využívá zásady pro povolení všech výjimek, které zakazují spuštění známého neautorizovaného (zakázaného) softwaru, a kontroluje a aktualizuje seznam neautorizovaných (zakázaných) softwarových programů ročně.

**ID**: 1196.01 l3organizational. 24 – 01. l **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Vzdálené ladění by mělo být pro API Apps vypnuté.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |Vzdálené ladění vyžaduje, aby byly pro aplikace API otevřené porty pro příchozí spojení. Vzdálené ladění by mělo být vypnuté. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-disables-bluetooth-and-peer-to-peer-networking-protocols-within-the-information-system-determined-to-be-unnecessary-or-non-secure"></a>Tato organizace zakazuje síťové protokoly Bluetooth a peer-to-peer v rámci informačního systému, který je určený jako nepotřebný nebo nezabezpečený.

**ID**: 1197.01 l3organizational. 3-01. l **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V počítačích by měly být povolené Adaptivní řízení aplikací pro definování bezpečných aplikací.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Povolit ovládací prvky aplikace, které definují seznam známých bezpečných aplikací spuštěných na vašich počítačích, a upozorní vás, když se spouštějí jiné aplikace. To pomáhá posílit zabezpečení vašich počítačů proti malwaru. Aby bylo možné zjednodušit proces konfigurace a údržby pravidel, Security Center využívá Machine Learning k analýze aplikací spuštěných na jednotlivých počítačích a k návrhu seznamu známých aplikací, které jsou v bezpečí. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

## <a name="segregation-in-networks"></a>Oddělení v sítích

### <a name="the-organizations-security-gateways-eg-firewalls-enforce-security-policies-and-are-configured-to-filter-traffic-between-domains-block-unauthorized-access-and-are-used-to-maintain-segregation-between-internal-wired-internal-wireless-and-external-network-segments-eg-the-internet-including-dmzs-and-enforce-access-control-policies-for-each-of-the-domains"></a>Brány zabezpečení organizace (např. brány firewall) vynutily zásady zabezpečení a jsou nakonfigurované pro filtrování provozu mezi doménami, blokují neoprávněný přístup a používají se k udržení oddělení mezi interními pevnými, interními bezdrátovými a externími segmenty sítě (např. Internet), včetně zóny DMZ a prosazování zásad řízení přístupu pro jednotlivé domény.

**ID**: 0805.01 m1organizational. 12.01. m **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[App Service by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Tato zásada Audituje jakékoli App Service, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Tato zásada Audituje jakékoli Container Registry, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |Audit, zakázáno |[1.0.0 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Tato zásada Audituje jakékoli Cosmos DB, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Centrum událostí by mělo používat koncový bod služby virtuální sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Tato zásada Audituje jakékoli centrum událostí, které není nakonfigurované na použití koncového bodu služby virtuální sítě. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Podsítě brány by se neměly konfigurovat se skupinou zabezpečení sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Tato zásada se zamítne, pokud je podsíť brány nakonfigurovaná pomocí skupiny zabezpečení sítě. Přiřazení skupiny zabezpečení sítě k podsíti brány způsobí, že brána přestane fungovat. |odmítnout |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Virtuální počítače s přístupem k Internetu by měly být chráněné pomocí skupin zabezpečení sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Chraňte své virtuální počítače před potenciálními hrozbami tím, že omezíte přístup k nim pomocí skupin zabezpečení sítě (NSG). Další informace o řízení provozu pomocí skupin zabezpečení sítě [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Tato zásada Audituje jakékoli Key Vault, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Tato zásada Audituje jakékoli Service Bus, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Tato zásada Audituje jakékoli SQL Server, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[Účty úložiště by měly používat koncový bod služby virtuální sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Tato zásada Audituje jakýkoliv účet úložiště, který není nakonfigurovaný tak, aby používal koncový bod služby virtuální sítě. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Podsítě by měly být přidružené ke skupině zabezpečení sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Chraňte svoji podsíť před potenciálními hrozbami tím, že omezíte přístup k ní pomocí skupiny zabezpečení sítě (NSG). Skupin zabezpečení sítě obsahují Access Control seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo zakazují síťový provoz do vaší podsítě. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuální počítače by měly být připojené ke schválené virtuální síti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Tato zásada Audituje všechny virtuální počítače připojené k virtuální síti, která není schválená. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="the-organizations-network-is-logically-and-physically-segmented-with-a-defined-security-perimeter-and-a-graduated-set-of-controls-including-subnetworks-for-publicly-accessible-system-components-that-are-logically-separated-from-the-internal-network-based-on-organizational-requirements-and-traffic-is-controlled-based-on-functionality-required-and-classification-of-the-datasystems-based-on-a-risk-assessment-and-their-respective-security-requirements"></a>Síť organizací je logicky a fyzicky rozdělená na vymezený hraniční zabezpečení a odstupňovanou sadu ovládacích prvků, včetně podsítí pro veřejně přístupné systémové komponenty, které jsou logicky oddělené od interní sítě, na základě požadavků organizace. a provoz se řídí podle požadovaných funkcí a klasifikace dat nebo systémů na základě posouzení rizik a jejich příslušných požadavků na zabezpečení.

**ID**: 0806.01 m2organizational. 12356-01. m **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[App Service by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Tato zásada Audituje jakékoli App Service, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Tato zásada Audituje jakékoli Container Registry, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |Audit, zakázáno |[1.0.0 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Tato zásada Audituje jakékoli Cosmos DB, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Centrum událostí by mělo používat koncový bod služby virtuální sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Tato zásada Audituje jakékoli centrum událostí, které není nakonfigurované na použití koncového bodu služby virtuální sítě. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Podsítě brány by se neměly konfigurovat se skupinou zabezpečení sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Tato zásada se zamítne, pokud je podsíť brány nakonfigurovaná pomocí skupiny zabezpečení sítě. Přiřazení skupiny zabezpečení sítě k podsíti brány způsobí, že brána přestane fungovat. |odmítnout |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Virtuální počítače s přístupem k Internetu by měly být chráněné pomocí skupin zabezpečení sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Chraňte své virtuální počítače před potenciálními hrozbami tím, že omezíte přístup k nim pomocí skupin zabezpečení sítě (NSG). Další informace o řízení provozu pomocí skupin zabezpečení sítě [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Tato zásada Audituje jakékoli Key Vault, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Tato zásada Audituje jakékoli Service Bus, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Tato zásada Audituje jakékoli SQL Server, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[Účty úložiště by měly používat koncový bod služby virtuální sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Tato zásada Audituje jakýkoliv účet úložiště, který není nakonfigurovaný tak, aby používal koncový bod služby virtuální sítě. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Podsítě by měly být přidružené ke skupině zabezpečení sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Chraňte svoji podsíť před potenciálními hrozbami tím, že omezíte přístup k ní pomocí skupiny zabezpečení sítě (NSG). Skupin zabezpečení sítě obsahují Access Control seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo zakazují síťový provoz do vaší podsítě. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuální počítače by měly být připojené ke schválené virtuální síti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Tato zásada Audituje všechny virtuální počítače připojené k virtuální síti, která není schválená. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="networks-are-segregated-from-production-level-networks-when-migrating-physical-servers-applications-or-data-to-virtualized-servers"></a>Sítě jsou při migraci fyzických serverů, aplikací nebo dat na virtualizované servery oddělené od sítí na úrovni služby.

**ID**: 0894.01 m2organizational. 7 – 01. m **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[App Service by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Tato zásada Audituje jakékoli App Service, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Tato zásada Audituje jakékoli Container Registry, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |Audit, zakázáno |[1.0.0 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Tato zásada Audituje jakékoli Cosmos DB, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Při vytváření virtuálních sítí nasadit sledovací proces sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9b99dd8-06c5-4317-8629-9d86a3c6e7d9) |Tato zásada vytvoří prostředek sledovacího procesu sítě v oblastech s virtuálními sítěmi. Je nutné zajistit existenci skupiny prostředků s názvem networkWatcherRG, která bude použita k nasazení instancí sledovacích procesů sítě. |DeployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Deploy.json) |
|[Centrum událostí by mělo používat koncový bod služby virtuální sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Tato zásada Audituje jakékoli centrum událostí, které není nakonfigurované na použití koncového bodu služby virtuální sítě. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Podsítě brány by se neměly konfigurovat se skupinou zabezpečení sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Tato zásada se zamítne, pokud je podsíť brány nakonfigurovaná pomocí skupiny zabezpečení sítě. Přiřazení skupiny zabezpečení sítě k podsíti brány způsobí, že brána přestane fungovat. |odmítnout |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Virtuální počítače s přístupem k Internetu by měly být chráněné pomocí skupin zabezpečení sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Chraňte své virtuální počítače před potenciálními hrozbami tím, že omezíte přístup k nim pomocí skupin zabezpečení sítě (NSG). Další informace o řízení provozu pomocí skupin zabezpečení sítě [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Tato zásada Audituje jakékoli Key Vault, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Tato zásada Audituje jakékoli Service Bus, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Tato zásada Audituje jakékoli SQL Server, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[Účty úložiště by měly používat koncový bod služby virtuální sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Tato zásada Audituje jakýkoliv účet úložiště, který není nakonfigurovaný tak, aby používal koncový bod služby virtuální sítě. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Podsítě by měly být přidružené ke skupině zabezpečení sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Chraňte svoji podsíť před potenciálními hrozbami tím, že omezíte přístup k ní pomocí skupiny zabezpečení sítě (NSG). Skupin zabezpečení sítě obsahují Access Control seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo zakazují síťový provoz do vaší podsítě. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuální počítače by měly být připojené ke schválené virtuální síti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Tato zásada Audituje všechny virtuální počítače připojené k virtuální síti, která není schválená. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

## <a name="network-connection-control"></a>Řízení připojení k síti

### <a name="network-traffic-is-controlled-in-accordance-with-the-organizations-access-control-policy-through-firewall-and-other-network-related-restrictions-for-each-network-access-point-or-external-telecommunication-services-managed-interface"></a>Síťový provoz se řídí v souladu se zásadami řízení přístupu organizací přes bránu firewall a dalšími omezeními týkajícími se sítě pro každý přístupový bod sítě nebo spravované rozhraní externí telekomunikační služby.

**ID**: 0809.01 n2organizational. 1234-01. n **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Doporučení adaptivního posílení zabezpečení sítě by se měla použít na internetových virtuálních počítačích.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analyzuje modely přenosů internetových virtuálních počítačů a poskytuje doporučení pro pravidla skupiny zabezpečení sítě, která omezují potenciální útok na plochu. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Aplikace API by měla být přístupná jen přes protokol HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Tato zásada Audituje všechny servery MySQL, které nevynucují připojení SSL. Azure Database for MySQL podporuje připojení Azure Database for MySQLho serveru k klientským aplikacím pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž" v prostředních prostředcích, a to šifrováním datového proudu mezi serverem a vaší aplikací. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Pro databázové servery PostgreSQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Tato zásada Audituje všechny PostgreSQL servery, které nevynucují připojení SSL. Azure Database for PostgreSQL upřednostňuje připojení klientských aplikací ke službě PostgreSQL pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž-in-the-middle" šifrováním datového proudu mezi serverem a vaší aplikací. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Function App by měl být přístupný jenom přes HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Virtuální počítače s přístupem k Internetu by měly být chráněné pomocí skupin zabezpečení sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Chraňte své virtuální počítače před potenciálními hrozbami tím, že omezíte přístup k nim pomocí skupin zabezpečení sítě (NSG). Další informace o řízení provozu pomocí skupin zabezpečení sítě [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[V aplikaci API by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[V Function App by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Ve vaší webové aplikaci by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditovat povolování jenom připojení přes SSL do Azure cache pro Redis. Použití zabezpečených připojení zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako jsou například prostředníky, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Měl by se povolit zabezpečený přenos do účtů úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Audit požadavek na zabezpečený přenos v účtu úložiště. Zabezpečený přenos je možnost, která vynutí, aby váš účet úložiště přijímal požadavky jenom od zabezpečených připojení (HTTPS). Použití protokolu HTTPS zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako je třeba prostředník, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Podsítě by měly být přidružené ke skupině zabezpečení sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Chraňte svoji podsíť před potenciálními hrozbami tím, že omezíte přístup k ní pomocí skupiny zabezpečení sítě (NSG). Skupin zabezpečení sítě obsahují Access Control seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo zakazují síťový provoz do vaší podsítě. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuální počítače by měly být připojené ke schválené virtuální síti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Tato zásada Audituje všechny virtuální počítače připojené k virtuální síti, která není schválená. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Webová aplikace by měla být přístupná jen přes protokol HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="transmitted-information-is-secured-and-at-a-minimum-encrypted-over-open-public-networks"></a>Přenesené informace jsou zabezpečené a jsou ve srovnání s otevřenými veřejnými sítěmi alespoň šifrované.

**ID**: 0810.01 n2organizational. 5-01. n **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Doporučení adaptivního posílení zabezpečení sítě by se měla použít na internetových virtuálních počítačích.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analyzuje modely přenosů internetových virtuálních počítačů a poskytuje doporučení pro pravidla skupiny zabezpečení sítě, která omezují potenciální útok na plochu. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Aplikace API by měla být přístupná jen přes protokol HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Tato zásada Audituje všechny servery MySQL, které nevynucují připojení SSL. Azure Database for MySQL podporuje připojení Azure Database for MySQLho serveru k klientským aplikacím pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž" v prostředních prostředcích, a to šifrováním datového proudu mezi serverem a vaší aplikací. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Pro databázové servery PostgreSQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Tato zásada Audituje všechny PostgreSQL servery, které nevynucují připojení SSL. Azure Database for PostgreSQL upřednostňuje připojení klientských aplikací ke službě PostgreSQL pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž-in-the-middle" šifrováním datového proudu mezi serverem a vaší aplikací. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Function App by měl být přístupný jenom přes HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Virtuální počítače s přístupem k Internetu by měly být chráněné pomocí skupin zabezpečení sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Chraňte své virtuální počítače před potenciálními hrozbami tím, že omezíte přístup k nim pomocí skupin zabezpečení sítě (NSG). Další informace o řízení provozu pomocí skupin zabezpečení sítě [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[V aplikaci API by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[V Function App by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Ve vaší webové aplikaci by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditovat povolování jenom připojení přes SSL do Azure cache pro Redis. Použití zabezpečených připojení zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako jsou například prostředníky, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Měl by se povolit zabezpečený přenos do účtů úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Audit požadavek na zabezpečený přenos v účtu úložiště. Zabezpečený přenos je možnost, která vynutí, aby váš účet úložiště přijímal požadavky jenom od zabezpečených připojení (HTTPS). Použití protokolu HTTPS zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako je třeba prostředník, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Podsítě by měly být přidružené ke skupině zabezpečení sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Chraňte svoji podsíť před potenciálními hrozbami tím, že omezíte přístup k ní pomocí skupiny zabezpečení sítě (NSG). Skupin zabezpečení sítě obsahují Access Control seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo zakazují síťový provoz do vaší podsítě. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuální počítače by měly být připojené ke schválené virtuální síti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Tato zásada Audituje všechny virtuální počítače připojené k virtuální síti, která není schválená. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Webová aplikace by měla být přístupná jen přes protokol HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="exceptions-to-the-traffic-flow-policy-are-documented-with-a-supporting-missionbusiness-need-duration-of-the-exception-and-reviewed-at-least-annually-traffic-flow-policy-exceptions-are-removed-when-no-longer-supported-by-an-explicit-missionbusiness-need"></a>Výjimky ze zásad toku provozu jsou popsány s podporou mise/obchodní potřeby, trvání výjimky a nejméně jednou ročně; výjimky zásad toku přenosů se odeberou, pokud už nejsou podporované explicitním posláním nebo obchodním potřebám.

**ID**: 0811.01 n2organizational. 6 – 01. n **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Doporučení adaptivního posílení zabezpečení sítě by se měla použít na internetových virtuálních počítačích.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analyzuje modely přenosů internetových virtuálních počítačů a poskytuje doporučení pro pravidla skupiny zabezpečení sítě, která omezují potenciální útok na plochu. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Aplikace API by měla být přístupná jen přes protokol HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Tato zásada Audituje všechny servery MySQL, které nevynucují připojení SSL. Azure Database for MySQL podporuje připojení Azure Database for MySQLho serveru k klientským aplikacím pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž" v prostředních prostředcích, a to šifrováním datového proudu mezi serverem a vaší aplikací. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Pro databázové servery PostgreSQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Tato zásada Audituje všechny PostgreSQL servery, které nevynucují připojení SSL. Azure Database for PostgreSQL upřednostňuje připojení klientských aplikací ke službě PostgreSQL pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž-in-the-middle" šifrováním datového proudu mezi serverem a vaší aplikací. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Function App by měl být přístupný jenom přes HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Virtuální počítače s přístupem k Internetu by měly být chráněné pomocí skupin zabezpečení sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Chraňte své virtuální počítače před potenciálními hrozbami tím, že omezíte přístup k nim pomocí skupin zabezpečení sítě (NSG). Další informace o řízení provozu pomocí skupin zabezpečení sítě [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[V aplikaci API by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[V Function App by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Ve vaší webové aplikaci by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditovat povolování jenom připojení přes SSL do Azure cache pro Redis. Použití zabezpečených připojení zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako jsou například prostředníky, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Měl by se povolit zabezpečený přenos do účtů úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Audit požadavek na zabezpečený přenos v účtu úložiště. Zabezpečený přenos je možnost, která vynutí, aby váš účet úložiště přijímal požadavky jenom od zabezpečených připojení (HTTPS). Použití protokolu HTTPS zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako je třeba prostředník, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Podsítě by měly být přidružené ke skupině zabezpečení sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Chraňte svoji podsíť před potenciálními hrozbami tím, že omezíte přístup k ní pomocí skupiny zabezpečení sítě (NSG). Skupin zabezpečení sítě obsahují Access Control seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo zakazují síťový provoz do vaší podsítě. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuální počítače by měly být připojené ke schválené virtuální síti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Tato zásada Audituje všechny virtuální počítače připojené k virtuální síti, která není schválená. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Webová aplikace by měla být přístupná jen přes protokol HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="remote-devices-establishing-a-non-remote-connection-are-not-allowed-to-communicate-with-external-remote-resources"></a>Vzdálená zařízení, která vytvářejí nevzdálené připojení, nemají povolenou komunikaci s externími (vzdálenými) prostředky.

**ID**: 0812.01 n2organizational. 8 – 01. n **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Doporučení adaptivního posílení zabezpečení sítě by se měla použít na internetových virtuálních počítačích.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analyzuje modely přenosů internetových virtuálních počítačů a poskytuje doporučení pro pravidla skupiny zabezpečení sítě, která omezují potenciální útok na plochu. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Aplikace API by měla být přístupná jen přes protokol HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Tato zásada Audituje všechny servery MySQL, které nevynucují připojení SSL. Azure Database for MySQL podporuje připojení Azure Database for MySQLho serveru k klientským aplikacím pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž" v prostředních prostředcích, a to šifrováním datového proudu mezi serverem a vaší aplikací. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Pro databázové servery PostgreSQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Tato zásada Audituje všechny PostgreSQL servery, které nevynucují připojení SSL. Azure Database for PostgreSQL upřednostňuje připojení klientských aplikací ke službě PostgreSQL pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž-in-the-middle" šifrováním datového proudu mezi serverem a vaší aplikací. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Function App by měl být přístupný jenom přes HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Virtuální počítače s přístupem k Internetu by měly být chráněné pomocí skupin zabezpečení sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Chraňte své virtuální počítače před potenciálními hrozbami tím, že omezíte přístup k nim pomocí skupin zabezpečení sítě (NSG). Další informace o řízení provozu pomocí skupin zabezpečení sítě [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[V aplikaci API by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[V Function App by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Ve vaší webové aplikaci by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditovat povolování jenom připojení přes SSL do Azure cache pro Redis. Použití zabezpečených připojení zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako jsou například prostředníky, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Měl by se povolit zabezpečený přenos do účtů úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Audit požadavek na zabezpečený přenos v účtu úložiště. Zabezpečený přenos je možnost, která vynutí, aby váš účet úložiště přijímal požadavky jenom od zabezpečených připojení (HTTPS). Použití protokolu HTTPS zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako je třeba prostředník, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Podsítě by měly být přidružené ke skupině zabezpečení sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Chraňte svoji podsíť před potenciálními hrozbami tím, že omezíte přístup k ní pomocí skupiny zabezpečení sítě (NSG). Skupin zabezpečení sítě obsahují Access Control seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo zakazují síťový provoz do vaší podsítě. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuální počítače by měly být připojené ke schválené virtuální síti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Tato zásada Audituje všechny virtuální počítače připojené k virtuální síti, která není schválená. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Webová aplikace by měla být přístupná jen přes protokol HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="the-ability-of-users-to-connect-to-the-internal-network-is-restricted-using-a-deny-by-default-and-allow-by-exception-policy-at-managed-interfaces-according-to-the-access-control-policy-and-the-requirements-of-clinical-and-business-applications"></a>Schopnost uživatelů připojit se k interní síti je omezená pomocí zásad pro řízení přístupu, které jsou ve spravovaných rozhraních a jsou v souladu se zásadami řízení přístupu, a požadavky klinických a obchodních aplikací.

**ID**: 0814.01 n1organizational. 12.01. n **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Doporučení adaptivního posílení zabezpečení sítě by se měla použít na internetových virtuálních počítačích.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analyzuje modely přenosů internetových virtuálních počítačů a poskytuje doporučení pro pravidla skupiny zabezpečení sítě, která omezují potenciální útok na plochu. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Aplikace API by měla být přístupná jen přes protokol HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Tato zásada Audituje všechny servery MySQL, které nevynucují připojení SSL. Azure Database for MySQL podporuje připojení Azure Database for MySQLho serveru k klientským aplikacím pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž" v prostředních prostředcích, a to šifrováním datového proudu mezi serverem a vaší aplikací. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Pro databázové servery PostgreSQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Tato zásada Audituje všechny PostgreSQL servery, které nevynucují připojení SSL. Azure Database for PostgreSQL upřednostňuje připojení klientských aplikací ke službě PostgreSQL pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž-in-the-middle" šifrováním datového proudu mezi serverem a vaší aplikací. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Function App by měl být přístupný jenom přes HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Virtuální počítače s přístupem k Internetu by měly být chráněné pomocí skupin zabezpečení sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Chraňte své virtuální počítače před potenciálními hrozbami tím, že omezíte přístup k nim pomocí skupin zabezpečení sítě (NSG). Další informace o řízení provozu pomocí skupin zabezpečení sítě [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[V aplikaci API by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[V Function App by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Ve vaší webové aplikaci by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditovat povolování jenom připojení přes SSL do Azure cache pro Redis. Použití zabezpečených připojení zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako jsou například prostředníky, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Měl by se povolit zabezpečený přenos do účtů úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Audit požadavek na zabezpečený přenos v účtu úložiště. Zabezpečený přenos je možnost, která vynutí, aby váš účet úložiště přijímal požadavky jenom od zabezpečených připojení (HTTPS). Použití protokolu HTTPS zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako je třeba prostředník, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Podsítě by měly být přidružené ke skupině zabezpečení sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Chraňte svoji podsíť před potenciálními hrozbami tím, že omezíte přístup k ní pomocí skupiny zabezpečení sítě (NSG). Skupin zabezpečení sítě obsahují Access Control seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo zakazují síťový provoz do vaší podsítě. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuální počítače by měly být připojené ke schválené virtuální síti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Tato zásada Audituje všechny virtuální počítače připojené k virtuální síti, která není schválená. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Webová aplikace by měla být přístupná jen přes protokol HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="user-identification-and-authentication"></a>Identifikace a ověřování uživatelů

### <a name="the-organization-ensures-that-redundant-user-ids-are-not-issued-to-other-users-and-that-all-users-are-uniquely-identified-and-authenticated-for-both-local-and-remote-access-to-information-systems"></a>Organizace zajistí, že se redundantní ID uživatelů nevydá ostatním uživatelům a že všichni uživatelé se jednoznačně identifikují a ověřují pro místní i vzdálený přístup k informačním systémům.

**ID**: 11109.01 q1organizational. 57-01. q **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) by mělo být povolené pro všechny účty předplatného s oprávněním vlastníka, aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="non-organizational-users-all-information-system-users-other-than-organizational-users-such-as-patients-customers-contractors-or-foreign-nationals-or-processes-acting-on-behalf-of-non-organizational-users-determined-to-need-access-to-information-residing-on-the-organizations-information-systems-are-uniquely-identified-and-authenticated"></a>Uživatelé mimo organizaci (všichni uživatelé s informacemi, kteří nejsou organizacemi uživatelů organizace, jako jsou pacienti, zákazníci, dodavatelé nebo cizí státní příslušníki) nebo procesy jednající jménem neorganizačních uživatelů, kteří jsou jednoznačně identifikováni a ověřováni, aby měli přístup k informacím, které se nacházejí v informačních systémech organizace.

**ID**: 11110.01 q1organizational. 6 – 01. **vlastnictví**q: nejde použít.

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V rámci vašeho předplatného by měly být povolené účty s oprávněním k zápisu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) by mělo být povolené pro všechny účty předplatného s oprávněním k zápisu, aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="when-pki-based-authentication-is-used-the-information-system-validates-certificates-by-constructing-and-verifying-a-certification-path-to-an-accepted-trust-anchor-including-checking-certificate-status-information-enforces-access-to-the-corresponding-private-key-maps-the-identity-to-the-corresponding-account-of-the-individual-or-group-and-implements-a-local-cache-of-revocation-data-to-support-path-discovery-and-validation-in-case-of-an-inability-to-access-revocation-information-via-the-network"></a>Při použití ověřování založeného na infrastruktuře PKI ověří informační systém certifikáty tak, že sestaví a ověří cestu k certifikaci přijaté kotvy vztahu důvěryhodnosti, včetně kontroly informací o stavu certifikátu. vynutil přístup k odpovídajícímu privátnímu klíči. mapuje identitu na odpovídající účet osoby nebo skupiny. a implementuje místní mezipaměť dat odvolání pro podporu zjišťování a ověřování cest v případě nemožnosti přístupu k informacím o odvolání přes síť.

**ID**: 11111.01 q2system. 4 – 01. q – **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |U všech účtů předplatného s oprávněním ke čtení by se mělo povolit Multi-Factor Authentication (MFA), aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-employs-replay-resistant-authentication-mechanisms-such-as-nonce-one-time-passwords-or-time-stamps-to-secure-network-access-for-privileged-accounts-and-for-hardware-token-based-authentication-employs-mechanisms-that-satisfy-minimum-token-requirements-discussed-in-nist-sp-800-63-2-electronic-authentication-guideline"></a>Informační systém využívá mechanismus ověřování odolný proti přehrání, jako je například hodnota nonce, jednorázová hesla nebo časová razítka pro zabezpečení přístupu k síti pro privilegované účty. v případě ověřování na základě hardwarových tokenů využívají mechanismy, které splňují požadavky na minimální tokeny popsané v NIST SP 800-63-2, na elektronický návod ověřování.

**ID**: 11112.01 q2organizational. 67-01. q **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Doporučuje se určit až 3 vlastníky předplatného, aby se snížila pravděpodobnost porušení napadeného vlastníka. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-requires-that-electronic-signatures-unique-to-one-individual-cannot-be-reused-by-or-reassigned-to-anyone-else"></a>Organizace vyžaduje, aby se elektronické podpisy, které jsou jedinečné pro jednoho jednotlivce, nepoužily nebo znovu nepřiřadily někomu jinému.

**ID**: 11208.01 q1organizational. 8 – 01. **vlastnictví**q: nejde použít.

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Doporučuje se určit více než jednoho vlastníka předplatného, aby měl přístup správce k redundanci. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="electronic-signatures-and-handwritten-signatures-executed-to-electronic-records-shall-be-linked-to-their-respective-electronic-records"></a>Elektronické podpisy a podpisové podpisy prováděné v elektronických záznamech jsou propojeny s příslušnými elektronickými záznamy.

**ID**: 11210.01 q2organizational. 10-01. q **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Auditovat počítače s Windows, které mají zadané členy ve skupině Administrators](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud místní skupina Administrators obsahuje jednoho nebo více členů uvedených v parametru zásad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="signed-electronic-records-shall-contain-information-associated-with-the-signing-in-human-readable-format"></a>Podepsané elektronické záznamy musí obsahovat informace spojené s podpisem v čitelném formátu.

**ID**: 11211.01 q2organizational. 11 – 01 **vlastnictví**q: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Audit počítačů s Windows chybějících zadaných členů ve skupině Administrators](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud místní skupina Administrators neobsahuje jednoho nebo více členů uvedených v parametru zásad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

### <a name="users-who-performed-privileged-functions-eg-system-administration-use-separate-accounts-when-performing-those-privileged-functions"></a>Uživatelé, kteří použili privilegované funkce (například Správa systému), při provádění těchto privilegovaných funkcí používají samostatné účty.

**ID**: 1123.01 q1system. 2-01. q **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Auditovat počítače s Windows, které mají další účty ve skupině Administrators](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud místní skupina Administrators obsahuje členy, kteří nejsou uvedeni v parametru zásad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |

### <a name="multi-factor-authentication-methods-are-used-in-accordance-with-organizational-policy-eg-for-remote-network-access"></a>Metody Multi-Factor Authentication se používají v souladu se zásadami organizace (například pro vzdálený přístup k síti).

**ID**: 1125.01 q2system. 1-01. q **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Auditovat počítače s Windows, které mají zadané členy ve skupině Administrators](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud místní skupina Administrators obsahuje jednoho nebo více členů uvedených v parametru zásad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="where-tokens-are-provided-for-multi-factor-authentication-in-person-verification-is-required-prior-to-granting-access"></a>Kde jsou pro službu Multi-Factor Authentication k dispozici tokeny, je před udělením přístupu vyžadováno ověření totožnosti osobně.

**ID**: 1127.01 q2system. 3-01. q **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Audit počítačů s Windows chybějících zadaných členů ve skupině Administrators](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud místní skupina Administrators neobsahuje jednoho nebo více členů uvedených v parametru zásad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

## <a name="identification-of-risks-related-to-external-parties"></a>Identifikace rizik souvisejících s externími stranami

### <a name="access-to-the-organizations-information-and-systems-by-external-parties-is-not-permitted-until-due-diligence-has-been-conducted-the-appropriate-controls-have-been-implemented-and-a-contractagreement-reflecting-the-security-requirements-is-signed-acknowledging-they-understand-and-accept-their-obligations"></a>Přístup k informacím o organizacích a systémům pomocí externích smluvních stran není povolený, dokud není provedena žádná opatrnost, byly implementovány příslušné ovládací prvky a smlouva nebo smlouva odrážející požadavky na zabezpečení jsou podepsané potvrzením, že tyto povinnosti znají a přijímají.

**ID**: 1401.05 i1organizational. 1239-05. i **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Měl by se povolit zabezpečený přenos do účtů úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Audit požadavek na zabezpečený přenos v účtu úložiště. Zabezpečený přenos je možnost, která vynutí, aby váš účet úložiště přijímal požadavky jenom od zabezpečených připojení (HTTPS). Použití protokolu HTTPS zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako je třeba prostředník, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="remote-access-connections-between-the-organization-and-external-parties-are-encrypted"></a>Připojení vzdáleného přístupu mezi organizací a externími stranami jsou šifrovaná.

**ID**: 1402.05 i1organizational. 45-05. i **vlastnictví**: nejde použít.

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Function App by měl být přístupný jenom přes HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |

### <a name="access-granted-to-external-parties-is-limited-to-the-minimum-necessary-and-granted-only-for-the-duration-required"></a>Přístup k externím stranám je omezený na minimum nezbytné a je možné ho udělit jenom pro požadovanou dobu trvání.

**ID**: 1403.05 i1organizational. 67-05. i **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Webová aplikace by měla být přístupná jen přes protokol HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="due-diligence-of-the-external-party-includes-interviews-document-review-checklists-certification-reviews-eg-hitrustnbspor-other-remote-means"></a>Dopředná opatrnost externí strany zahrnuje rozhovory, recenze dokumentů, kontrolní seznamy, kontroly certifikace (např. HITRUST) &nbsp; nebo jiné vzdálené prostředky.

**ID**: 1404.05 i2organizational. 1-05. i **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Aplikace API by měla být přístupná jen přes protokol HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |

### <a name="the-identification-of-risks-related-to-external-party-access-takes-into-account-a-minimal-set-of-specifically-defined-issues"></a>Identifikace rizik spojených s přístupem k externí straně bere v úvahu minimální sadu konkrétně definovaných problémů.

**ID**: 1418.05 i1organizational. 8-05. i **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Tato zásada Audituje všechny servery MySQL, které nevynucují připojení SSL. Azure Database for MySQL podporuje připojení Azure Database for MySQLho serveru k klientským aplikacím pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž" v prostředních prostředcích, a to šifrováním datového proudu mezi serverem a vaší aplikací. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-organization-obtains-satisfactory-assurances-that-reasonable-information-security-exists-across-their-information-supply-chain-by-performing-an-annual-review-which-includes-all-partnersthird-party-providers-upon-which-their-information-supply-chain-depends"></a>Organizace získá uspokojivou záruku, že v rámci svých informačních dodavatelských řetězců existují rozumné bezpečnostní údaje, které provádějí roční přezkum, který zahrnuje všechny partnery/poskytovatelé třetích stran, na kterých závisí jejich dodavatelský řetězec.

**ID**: 1450.05 i2organizational. 2-05. i **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro databázové servery PostgreSQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Tato zásada Audituje všechny PostgreSQL servery, které nevynucují připojení SSL. Azure Database for PostgreSQL upřednostňuje připojení klientských aplikací ke službě PostgreSQL pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž-in-the-middle" šifrováním datového proudu mezi serverem a vaší aplikací. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="cloud-service-providers-design-and-implement-controls-to-mitigate-and-contain-data-security-risks-through-proper-separation-of-duties-role-based-access-and-least-privilege-access-for-all-personnel-within-their-supply-chain"></a>Poskytovatelé cloudových služeb navrhují a implementují ovládací prvky, které umožňují zmírnit a zahrnovat rizika zabezpečení dat prostřednictvím správného oddělení povinností, přístupu na základě role a přístupu k minimálnímu oprávnění pro všechny zaměstnance v rámci jejich dodavatelského řetězce.

**ID**: 1451.05 icsporganizational. 2-05. i **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditovat povolování jenom připojení přes SSL do Azure cache pro Redis. Použití zabezpečených připojení zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako jsou například prostředníky, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="audit-logging"></a>Protokolování auditu

### <a name="a-secure-audit-record-is-created-for-all-activities-on-the-system-create-read-update-delete-involving-covered-information"></a>Pro všechny aktivity v systému se vytvoří zabezpečený záznam auditu (vytvořit, číst, aktualizovat, odstranit) včetně zahrnutých informací.

**ID**: 1202.09 aa1system. 1-09. AA **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Měly by být povolené diagnostické protokoly v Azure Data Lake Store.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Audituje povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Musí být nainstalované aktualizace systému ve virtuálních počítačích Virtual Machine Scale Sets.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Auditujte, jestli existují chybějící aktualizace zabezpečení systému a důležité aktualizace, které byste měli nainstalovat, abyste měli jistotu, že jsou vaše virtuální počítače s Windows a Linux zabezpečené. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |

### <a name="audit-records-include-the-unique-user-id-unique-data-subject-id-function-performed-and-datetime-the-event-was-performed"></a>Záznamy auditu zahrnují jedinečné ID uživatele, jedinečné ID subjektu dat, prováděnou funkci a datum a čas provedení události.

**ID**: 1203.09 aa1system. 2 – 09. AA **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Měly by být povolené diagnostické protokoly v Logic Apps.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Audituje povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |

### <a name="the-activities-of-privileged-users-administrators-operators-etc-include-the-successfailure-of-the-event-time-the-event-occurred-the-account-involved-the-processes-involved-and-additional-information-about-the-event"></a>Aktivity privilegovaných uživatelů (správců, operátorů atd.) zahrnují úspěch nebo neúspěch události, čas výskytu události, související účet, procesy a další informace o události.

**ID**: 1204.09 aa1system. 3-09. AA **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Měly by být povolené diagnostické protokoly v IoT Hub.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Audituje povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

### <a name="logs-of-messages-sent-and-received-are-maintained-including-the-date-time-origin-and-destination-of-the-message-but-not-its-contents"></a>Protokoly odesílaných a přijatých zpráv se uchovávají včetně data, času, původu a cíle zprávy, ale ne jejího obsahu.

**ID**: 1205.09 aa2system. 1-09. AA **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V účtech Batch by měly být povolené diagnostické protokoly.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Audituje povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |

### <a name="auditing-is-always-available-while-the-system-is-active-and-tracks-key-events-successfailed-data-access-system-security-configuration-changes-privileged-or-utility-use-any-alarms-raised-nbspactivation-and-de-activation-of-protection-systems-eg-av-and-ids-activation-and-deactivation-of-identification-and-authentication-mechanisms-and-creation-and-deletion-of-system-level-objects"></a>Auditování je vždy k dispozici, když je systém aktivní a sleduje klíčové události, úspěšný/neúspěšný přístup k datům, změny konfigurace zabezpečení systému, privilegovaného nebo utility použití, všechna vyvolaná alarmy, &nbsp; Aktivace a deaktivace systémů ochrany (např. a/V a ID), aktivaci a deaktivaci ověřovacích mechanismů a vytváření a mazání objektů na úrovni systému.

**ID**: 1206.09 aa2system. 23 – 09. AA **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Měly by být povolené diagnostické protokoly v Virtual Machine Scale Sets.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Doporučuje se povolit protokoly, aby se záznam aktivity mohl znovu vytvořit, když se v případě incidentu nebo ohrožení vyžaduje šetření. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

### <a name="audit-records-are-retained-for-90-days-and-older-audit-records-are-archived-for-one-year"></a>Záznamy auditu se uchovávají po dobu 90 dnů a starší záznamy auditu se archivují po dobu jednoho roku.

**ID**: 1207.09 aa2system. 4 – 09. AA **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Měly by být povolené diagnostické protokoly v Azure Stream Analytics.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Audituje povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Měly by být povolené diagnostické protokoly v centru událostí.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Audituje povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

### <a name="audit-logs-are-maintained-for-management-activities-system-and-application-startupshutdownerrors-file-changes-and-security-policy-changes"></a>Protokoly auditu se udržují pro aktivity správy, spuštění systému a aplikace, chyby, změny souborů a změny zásad zabezpečení.

**ID**: 1208.09 aa3system. 1-09. AA **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Měly by být povolené diagnostické protokoly ve vyhledávacích službách.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Audituje povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Měly by být povolené diagnostické protokoly v Service Bus.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Audituje povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

### <a name="the-information-system-generates-audit-records-containing-the-following-detailed-information-i-filename-accessed-ii-program-or-command-used-to-initiate-the-event-and-iii-source-and-destination-addresses"></a>Informační systém generuje záznamy auditu obsahující následující podrobné informace: (i) název souboru, ke kterému se používá; (II) program nebo příkaz, který se používá k inicializaci události; a (III) zdrojové a cílové adresy.

**ID**: 1209.09 aa3system. 2 – 09. AA **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Měly by být povolené diagnostické protokoly v App Services.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Audituje povolení diagnostických protokolů v aplikaci. Díky tomu můžete znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |

### <a name="all-disclosures-of-covered-information-within-or-outside-of-the-organization-are-logged-including-type-of-disclosure-datetime-of-the-event-recipient-and-sender"></a>Všechna zveřejnění zahrnutých informací v rámci organizace i mimo ni jsou protokolována včetně typu zveřejnění, data a času události, příjemce a odesilatele.

**ID**: 1210.09 aa3system. 3-09. AA – **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Auditování nastavení diagnostiky](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |Auditování nastavení diagnostiky pro vybrané typy prostředků |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Měly by být povolené diagnostické protokoly v Data Lake Analytics.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Audituje povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |

### <a name="the-organization-verifies-every-ninety-90-days-for-each-extract-of-covered-information-recorded-that-the-data-is-erased-or-its-use-is-still-required"></a>Tato organizace ověřuje každé 90 (90) dnů pro každý výpis informací zaznamenaných v případě, že jsou data smazána nebo je stále vyžadováno jejich použití.

**ID**: 1211.09 aa3system. 4 – 09. AA – **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Auditování na SQL serveru by mělo být povolené.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Auditování v SQL Server by mělo být povoleno ke sledování databázových činností napříč všemi databázemi na serveru a jejich uložení v protokolu auditu. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Měly by být povolené diagnostické protokoly v Key Vault.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Audituje povolení diagnostických protokolů. Díky tomu můžete znovu vytvořit stopy aktivity, které se použijí pro účely šetření, když dojde k bezpečnostnímu incidentu nebo když dojde k ohrožení zabezpečení sítě. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

## <a name="monitoring-system-use"></a>Monitorování využití systému

### <a name="unauthorized-remote-connections-to-the-information-systems-are-monitored-and-reviewed-at-least-quarterly-and-appropriate-action-is-taken-if-an-unauthorized-connection-is-discovered"></a>Nepovolená vzdálená připojení k informačním systémům jsou monitorována a přezkoumána nejméně čtvrtletně a při zjištění neoprávněného připojení je provedena příslušná opatření.

**ID**: 1120.09 ab3system. 9-09. AB **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Azure Monitor by měly shromažďovat protokoly aktivit ze všech oblastí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Tato zásada Audituje profil Azure Monitorho protokolu, který neexportuje aktivity ze všech podporovaných oblastí Azure, včetně globálního. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="the-organization-monitors-the-information-system-to-identify-irregularities-or-anomalies-that-are-indicators-of-a-system-malfunction-or-compromise-and-help-confirm-the-system-is-functioning-in-an-optimal-resilient-and-secure-state"></a>Organizace monitoruje informační systém k identifikaci nedovolených nebo anomálií, které jsou indikátory selhání systému nebo ohrožení zabezpečení, a umožňují potvrdit, že systém funguje v optimálním, odolném a zabezpečeném stavu.

**ID**: 12100.09 ab2system. 15-09. AB **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Agent Log Analytics by měl být nainstalovaný na virtuálních počítačích](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Tato zásada Audituje všechny virtuální počítače se systémem Windows/Linux, pokud není nainstalován agent Log Analytics. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-specifies-how-often-audit-logs-are-reviewed-how-the-reviews-are-documented-and-the-specific-roles-and-responsibilities-of-the-personnel-conducting-the-reviews-including-the-professional-certifications-or-other-qualifications-required"></a>Organizace určuje, jak často se protokoly auditu kontrolují, jak jsou popsané recenze, a konkrétní role a zodpovědnosti pracovníků, kteří provádějí kontroly, včetně profesionálních certifikací nebo jiných požadovaných kvalifikací.

**ID**: 12101.09 ab1organizational. 3-09. AB **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Agent Log Analytics by měl být nainstalovaný na Virtual Machine Scale Sets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Tato zásada Audituje jakékoli Virtual Machine Scale Sets systému Windows nebo Linux, pokud není nainstalován agent Log Analytics. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-shall-periodically-test-its-monitoring-and-detection-processes-remediate-deficiencies-and-improve-its-processes"></a>Organizace pravidelně testuje procesy monitorování a zjišťování, napravují nedostatky a zdokonaluje její procesy.

**ID**: 12102.09 ab1organizational. 4-09 **vlastnictví**AB: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Audit počítačů s Windows, na kterých je agent Log Analytics nepřipojený podle očekávání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud není nainstalován agent nebo pokud je nainstalován, ale objekt COM AgentConfigManager. MgmtSvcCfg vrátí, že je zaregistrován pro jiný pracovní prostor, než je ID zadané v parametru zásad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="all-applicable-legal-requirements-related-to-monitoring-authorized-access-and-unauthorized-access-attempts-are-met"></a>Všechny platné zákonné požadavky týkající se monitorování oprávněného přístupu a neautorizovaných pokusů o přístup jsou splněné.

**ID**: 1212.09 ab1system. 1-09. AB **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Profil protokolu Azure Monitor by měl shromažďovat protokoly pro kategorie Write, DELETE a Action.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Tato zásada zajišťuje, že profil protokolu shromažďuje protokoly pro kategorie Write, DELETE a Action. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="automated-systems-deployed-throughout-the-organizations-environment-are-used-to-monitor-key-events-and-anomalous-activity-and-analyze-system-logs-the-results-of-which-are-reviewed-regularly"></a>Automatizované systémy nasazené v prostředí organizace se používají ke sledování klíčových událostí a aktivit neobvyklé a k analýze systémových protokolů. výsledky se pravidelně kontrolují.

**ID**: 1213.09 ab2system. 128-09. AB **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V předplatném by mělo být povolené Automatické zřizování agenta monitorování Log Analytics.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Povolení automatického zřizování agenta monitorování Log Analytics za účelem shromažďování dat zabezpečení |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="monitoring-includes-privileged-operations-authorized-access-or-unauthorized-access-attempts-including-attempts-to-access-deactivated-accounts-and-system-alerts-or-failures"></a>Monitorování zahrnuje privilegované operace, autorizovaný přístup nebo neoprávněné pokusy o přístup, včetně pokusů o přístup k deaktivovaným účtům a upozornění systému nebo selhání.

**ID**: 1214.09 ab2system. 3456-09. AB **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Azure Monitor by měly shromažďovat protokoly aktivit ze všech oblastí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Tato zásada Audituje profil Azure Monitorho protokolu, který neexportuje aktivity ze všech podporovaných oblastí Azure, včetně globálního. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="auditing-and-monitoring-systems-employed-by-the-organization-support-audit-reduction-and-report-generation"></a>Systémy auditování a monitorování používané organizací podporují omezování auditu a generování sestav.

**ID**: 1215.09 ab2system. 7 – 09. AB **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Agent Log Analytics by měl být nainstalovaný na virtuálních počítačích](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Tato zásada Audituje všechny virtuální počítače se systémem Windows/Linux, pokud není nainstalován agent Log Analytics. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="automated-systems-are-used-to-review-monitoring-activities-of-security-systems-eg-ipsids-and-system-records-on-a-daily-basis-and-identify-and-document-anomalies"></a>Automatizované systémy se používají ke kontrole monitorovacích aktivit systémů zabezpečení (například IP adresy/identifikátory) a systémových záznamů denně a k identifikaci a dokumentaci anomálií.

**ID**: 1216.09 ab3system. 12 – 09. AB **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Agent Log Analytics by měl být nainstalovaný na Virtual Machine Scale Sets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Tato zásada Audituje jakékoli Virtual Machine Scale Sets systému Windows nebo Linux, pokud není nainstalován agent Log Analytics. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="alerts-are-generated-for-technical-personnel-to-analyze-and-investigate-suspicious-activity-or-suspected-violations"></a>Pro technické pracovníky se generují výstrahy pro analýzu a vyšetřování podezřelé aktivity nebo podezřelých porušení.

**ID**: 1217.09 ab3system. 3-09. AB **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Audit počítačů s Windows, na kterých je agent Log Analytics nepřipojený podle očekávání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud není nainstalován agent nebo pokud je nainstalován, ale objekt COM AgentConfigManager. MgmtSvcCfg vrátí, že je zaregistrován pro jiný pracovní prostor, než je ID zadané v parametru zásad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="the-information-system-is-able-to-automatically-process-audit-records-for-events-of-interest-based-on-selectable-criteria"></a>Informační systém dokáže automaticky zpracovat záznamy auditu pro události, které mají zájem na základě volitelných kritérií.

**ID**: 1219.09 ab3system. 10-09. AB **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Profil protokolu Azure Monitor by měl shromažďovat protokoly pro kategorie Write, DELETE a Action.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Tato zásada zajišťuje, že profil protokolu shromažďuje protokoly pro kategorie Write, DELETE a Action. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="monitoring-includes-inbound-and-outbound-communications-and-file-integrity-monitoring"></a>Monitorování zahrnuje příchozí a odchozí komunikaci a monitorování integrity souborů.

**ID**: 1220.09 ab3system. 56-09. AB **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V předplatném by mělo být povolené Automatické zřizování agenta monitorování Log Analytics.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Povolení automatického zřizování agenta monitorování Log Analytics za účelem shromažďování dat zabezpečení |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

## <a name="administrator-and-operator-logs"></a>Protokoly správců a operátorů

### <a name="the-organization-ensures-proper-logging-is-enabled-in-order-to-audit-administrator-activities-and-reviews-system-administrator-and-operator-logs-on-a-regular-basis"></a>Organizace zajišťuje správné protokolování, aby mohl auditovat činnosti správců. a pravidelně kontroluje správce a obsluha systému.

**ID**: 1270.09 ad1system. 12 – 09.AD **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace správy by měla existovat výstraha protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Tato zásada Audituje konkrétní operace správy bez nakonfigurovaného nastavení výstrahy protokolu aktivit. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="an-intrusion-detection-system-managed-outside-of-the-control-of-system-and-network-administrators-is-used-to-monitor-system-and-network-administration-activities-for-compliance"></a>Systém zjišťování neoprávněných vniknutí spravovaný mimo kontrolu systémových a síťových správců se používá k monitorování dodržování předpisů pro činnosti správy systému a sítě.

**ID**: 1271.09 ad1system. 1-09.AD **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro konkrétní operace správy by měla existovat výstraha protokolu aktivit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Tato zásada Audituje konkrétní operace správy bez nakonfigurovaného nastavení výstrahy protokolu aktivit. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

## <a name="segregation-of-duties"></a>Oddělení povinností

### <a name="separation-of-duties-is-used-to-limit-the-risk-of-unauthorized-or-unintentional-modification-of-information-and-systems"></a>Oddělení cel slouží k omezení rizika neoprávněné nebo neúmyslné úpravy informací a systémů.

**ID**: 1229.09 c1organizational. 1-09. **vlastnictví**c: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro služby Kubernetes by se měla použít Access Control na základě rolí (RBAC).](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |K zajištění podrobného filtrování akcí, které mohou uživatelé provádět, použijte Access Control na základě rolí (RBAC) ke správě oprávnění v clusterech služby Kubernetes a ke konfiguraci relevantních zásad autorizace. |Audit, zakázáno |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="no-single-person-is-able-to-access-modify-or-use-information-systems-without-authorization-or-detection"></a>Žádná jediná osoba nemůže přistupovat k informačním systémům, měnit je ani používat, aniž by bylo možné provádět autorizaci nebo detekci.

**ID**: 1230.09 c2organizational. 1-09. **vlastnictví**c: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Auditovat využití vlastních pravidel RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Místo vlastních rolí RBAC, které jsou náchylné k chybám, byste měli auditovat předdefinované role, jako je vlastník, přispěvatel, čtenář. Použití vlastních rolí se považuje za výjimku a vyžaduje přísné kontroly a modelování hrozeb. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

### <a name="access-for-individuals-responsible-for-administering-nbspaccess-controls-is-limited-to-the-minimum-necessary-based-upon-each-users-role-and-responsibilities-and-these-individuals-cannot-access-audit-functions-related-to-these-controls"></a>Přístup jednotlivců zodpovědný za správu &nbsp; řízení přístupu je omezený na minimum, které je nezbytné na základě role a odpovědností jednotlivých uživatelů, a tito jednotlivci nemají přístup k funkcím auditu, které souvisejí s těmito ovládacími prvky.

**ID**: 1232.09 c3organizational. 12. **vlastnictví**c: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Počítače s Windows by měly splňovat požadavky na přiřazení uživatelských práv.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe068b215-0026-4354-b347-8fb2766f73a2) |Počítače s Windows by měly mít zadané nastavení Zásady skupiny v kategorii přiřazení uživatelských práv k povolení místního přihlášení, protokolu RDP, přístupu ze sítě a mnoha dalších uživatelských aktivit. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_UserRightsAssignment_AINE.json) |

### <a name="security-audit-activities-are-independent"></a>Aktivity auditu zabezpečení jsou nezávislé.

**ID**: 1276.09 c2organizational. 2 – 09. **vlastnictví**c: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Vlastní role vlastníka předplatného by neměly existovat.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Tato zásada zajišťuje, že žádné vlastní role vlastníka předplatného neexistují. |Audit, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

### <a name="the-initiation-of-an-event-is-separated-from-its-authorization-to-reduce-the-possibility-of-collusion"></a>Zahájení události je oddělené od jeho autorizace, aby se snížila možnost tajnou dohodu.

**ID**: 1277.09 c2organizational. 4 – 09 **vlastnictví**c: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Počítače s Windows by měly splňovat požadavky na možnosti zabezpečení – řízení uživatelských účtů.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F492a29ed-d143-4f03-b6a4-705ce081b463) |Počítače s Windows by měly mít zadaná Zásady skupiny nastavení v kategorii možnosti zabezpečení – řízení uživatelských účtů pro správu, chování výzvy ke zvýšení oprávnění a virtualizaci selhání zápisu souborů a registrů. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsUserAccountControl_AINE.json) |

### <a name="the-organization-identifies-duties-that-require-separation-and-defines-information-system-access-authorizations-to-support-separation-of-duties-and-incompatible-duties-are-segregated-across-multiple-users-to-minimize-the-opportunity-for-misuse-or-fraud"></a>Organizace identifikuje povinnosti, které vyžadují oddělení, a definuje autorizaci přístupu k systémovým údajům pro podporu rozdělení povinností. a nekompatibilní povinnosti jsou oddělené napříč více uživateli, aby se minimalizovala možnost zneužití nebo podvodů.

**ID**: 1278.09 c2organizational. 56-09. **vlastnictví**v jazyce c: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Vlastní role vlastníka předplatného by neměly existovat.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Tato zásada zajišťuje, že žádné vlastní role vlastníka předplatného neexistují. |Audit, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="controls-against-malicious-code"></a>Ovládací prvky proti škodlivému kódu

### <a name="anti-virus-and-anti-spyware-are-installed-operating-and-updated-on-all-end-user-devices-to-conduct-periodic-scans-of-the-systems-to-identify-and-remove-unauthorized-software-server-environments-for-which-the-server-software-developer-specifically-recommends-not-installing-host-based-anti-virus-and-anti-spyware-software-may-address-the-requirement-via-a-network-based-malware-detection-nbmd-solution"></a>Proti virům a spywaru jsou nainstalovány, provozovány a aktualizovány na všech zařízeních koncových uživatelů, aby prováděla pravidelné kontroly systémů, které identifikují a odstraňují neautorizovaný software. Prostředí serveru, pro které vývojář serverového softwaru konkrétně nedoporučuje instalovat antivirový a antispywarový software, může požadavek vyřešit prostřednictvím řešení zjišťování malwaru (NBMD) založeného na síti.

**ID**: 0201.09 j1organizational. 124 – 09. j **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V počítačích by měly být povolené Adaptivní řízení aplikací pro definování bezpečných aplikací.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Povolit ovládací prvky aplikace, které definují seznam známých bezpečných aplikací spuštěných na vašich počítačích, a upozorní vás, když se spouštějí jiné aplikace. To pomáhá posílit zabezpečení vašich počítačů proti malwaru. Aby bylo možné zjednodušit proces konfigurace a údržby pravidel, Security Center využívá Machine Learning k analýze aplikací spuštěných na jednotlivých počítačích a k návrhu seznamu známých aplikací, které jsou v bezpečí. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Nasadit výchozí rozšíření Microsoft IaaSAntimalware pro Windows Server](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2835b622-407b-4114-9198-6f7064cbe0dc) |Tato zásada nasadí rozšíření Microsoft IaaSAntimalware s výchozí konfigurací, pokud není virtuální počítač nakonfigurovaný s antimalwarovým rozšířením. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |
|[Řešení ochrany koncových bodů by se mělo nainstalovat na Virtual Machine Scale Sets.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Auditujte existenci a stav řešení ochrany koncových bodů na virtuálních počítačích, které jsou v sadě škálování, a chránit je před hrozbami a ohroženími zabezpečení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft Antimalware pro Azure by měl být nakonfigurovaný tak, aby automaticky aktualizoval podpisy ochrany.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |Tato zásada Audituje všechny virtuální počítače s Windows, které nejsou nakonfigurované pomocí automatických aktualizací podpisů Microsoft antimalwarové ochrany. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |
|[Monitorovat chybějící Endpoint Protection v Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servery bez nainstalovaného agenta Endpoint Protection se budou monitorovat Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[Na počítače by se měly nainstalovat aktualizace systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Chybějící aktualizace systému zabezpečení na vašich serverech se monitorují Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

## <a name="back-up"></a>Zálohování

### <a name="backup-copies-of-information-and-software-are-made-and-tests-of-the-media-and-restoration-procedures-are-regularly-performed-at-appropriate-intervals"></a>K dispozici jsou záložní kopie informací a softwaru a testy médií a postupů obnovení se pravidelně provádějí v příslušných intervalech.

**ID**: 1616.09 l1organizational. 16-09. l **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro databáze SQL Azure by mělo být povolené dlouhodobé geograficky redundantní zálohování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Tato zásada Audituje jakékoli Azure SQL Database s dlouhodobou geograficky redundantní zálohou, která není povolená. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="a-formal-definition-of-the-level-of-backup-required-for-each-system-is-defined-and-documented-including-how-each-system-will-be-restored-the-scope-of-data-to-be-imaged-frequency-of-imaging-and-duration-of-retention-based-on-relevant-contractual-legal-regulatory-and-business-requirements"></a>Formální definice úrovně zálohování, která je potřeba pro jednotlivé systémy, je definovaná a zdokumentovaná, včetně toho, jak se jednotlivé systémy obnoví, rozsah dat, která se mají image, četnost imagí a doba trvání uchovávání na základě relevantních smluvních, právních, regulativních a podnikových požadavků.

**ID**: 1617.09 l1organizational. 23 – 09. l **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro Azure Database for MySQL by měla být povolená geograficky redundantní záloha.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Tato zásada Audituje jakékoli Azure Database for MySQL s geograficky redundantní zálohou, která není povolená. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="the-backups-are-stored-in-a-physically-secure-remote-location-at-a-sufficient-distance-to-make-them-reasonably-immune-from-damage-to-data-at-the-primary-site-and-reasonable-physical-and-environmental-controls-are-in-place-to-ensure-their-protection-at-the-remote-location"></a>Zálohy jsou uložené v fyzicky zabezpečeném vzdáleném umístění, aby byly dostatečně odolné proti poškození dat v primární lokalitě a aby byla zajištěna jejich ochrana ve vzdáleném umístění.

**ID**: 1618.09 l1organizational. 45-09. l **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro Azure Database for PostgreSQL by měla být povolená geograficky redundantní záloha.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Tato zásada Audituje jakékoli Azure Database for PostgreSQL s geograficky redundantní zálohou, která není povolená. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="inventory-records-for-the-backup-copies-including-content-and-current-location-are-maintained"></a>Zachovají se záznamy inventáře záložních kopií, včetně obsahu a aktuálního umístění.

**ID**: 1619.09 l1organizational. 7 – 09. l **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro Azure Database for MariaDB by měla být povolená geograficky redundantní záloha.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Tato zásada Audituje jakékoli Azure Database for MariaDB s geograficky redundantní zálohou, která není povolená. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="when-the-backup-service-is-delivered-by-the-third-party-the-service-level-agreement-includes-the-detailed-protections-to-control-confidentiality-integrity-and-availability-of-the-backup-information"></a>Po doručení služby zálohování třetí stranou zahrnuje Smlouva o úrovni služeb podrobnou ochranu pro kontrolu důvěrnosti, integrity a dostupnosti informací o zálohování.

**ID**: 1620.09 l1organizational. 8 – 09. l **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Azure Backup by měla být povolená Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Zajistěte ochranu Virtual Machines Azure tím, že povolíte Azure Backup. Azure Backup je bezpečné a nákladově efektivní řešení ochrany dat pro Azure. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="automated-tools-are-used-to-track-all-backups"></a>Automatizované nástroje se používají ke sledování všech záloh.

**ID**: 1621.09 l2organizational. 1-09. l **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro databáze SQL Azure by mělo být povolené dlouhodobé geograficky redundantní zálohování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Tato zásada Audituje jakékoli Azure SQL Database s dlouhodobou geograficky redundantní zálohou, která není povolená. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="the-integrity-and-security-of-the-backup-copies-are-maintained-to-ensure-future-availability-and-any-potential-accessibility-problems-with-the-backup-copies-are-identified-and-mitigated-in-the-event-of-an-area-wide-disaster"></a>Integrita a zabezpečení záložních kopií jsou zachovány, aby se zajistila budoucí dostupnost, a všechny potenciální problémy s přístupností záložních kopií se identifikují a zmírnit v případě havárie v celé oblasti.

**ID**: 1622.09 l2organizational. 23 – 09. l **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro Azure Database for MySQL by měla být povolená geograficky redundantní záloha.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Tato zásada Audituje jakékoli Azure Database for MySQL s geograficky redundantní zálohou, která není povolená. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="covered-information-is-backed-up-in-an-encrypted-format-to-ensure-confidentiality"></a>Zahrnuté informace jsou zálohované v šifrovaném formátu, aby se zajistila důvěrnost.

**ID**: 1623.09 l2organizational. 4 – 09. l **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro Azure Database for PostgreSQL by měla být povolená geograficky redundantní záloha.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Tato zásada Audituje jakékoli Azure Database for PostgreSQL s geograficky redundantní zálohou, která není povolená. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-performs-incremental-or-differential-backups-daily-and-full-backups-weekly-to-separate-media"></a>Organizace provádí každý týden přírůstkové nebo rozdílové zálohování denně a úplných záloh na samostatné médium.

**ID**: 1624.09 l3organizational. 12 – 09. l **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro Azure Database for MariaDB by měla být povolená geograficky redundantní záloha.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Tato zásada Audituje jakékoli Azure Database for MariaDB s geograficky redundantní zálohou, která není povolená. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="three-3-generations-of-backups-full-plus-all-related-incremental-or-differential-backups-are-stored-off-site-and-both-on-site-and-off-site-backups-are-logged-with-name-date-time-and-action"></a>Tři (3) generace záloh (úplné plus všechny související přírůstkové nebo rozdílové zálohy) se ukládají mimo lokalitu a zálohy na pracovišti i mimo pracoviště se zaznamenávají s názvem, datem, časem a akcí.

**ID**: 1625.09 l3organizational. 34-09. l **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Azure Backup by měla být povolená Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Zajistěte ochranu Virtual Machines Azure tím, že povolíte Azure Backup. Azure Backup je bezpečné a nákladově efektivní řešení ochrany dat pro Azure. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="the-organization-ensures-a-current-retrievable-copy-of-covered-information-is-available-before-movement-of-servers"></a>Organizace zajišťuje, aby byla k dispozici aktuální dostupná kopie zahrnutých informací před přesunem serverů.

**ID**: 1626.09 l3organizational. 5 – 09. l **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro Azure Database for PostgreSQL by měla být povolená geograficky redundantní záloha.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Tato zásada Audituje jakékoli Azure Database for PostgreSQL s geograficky redundantní zálohou, která není povolená. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-tests-backup-information-following-each-backup-to-verify-media-reliability-and-information-integrity-and-at-least-annually-thereafter"></a>Organizace testuje informace o zálohování za každou zálohou za účelem ověření spolehlivosti a integrity informací o médiích a nejméně po měsících.

**ID**: 1627.09 l3organizational. 6 – 09. l **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro Azure Database for MariaDB by měla být povolená geograficky redundantní záloha.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Tato zásada Audituje jakékoli Azure Database for MariaDB s geograficky redundantní zálohou, která není povolená. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="workforce-members-roles-and-responsibilities-in-the-data-backup-process-are-identified-and-communicated-to-the-workforce-in-particular-bring-your-own-device-byod-users-are-required-to-perform-backups-of-organizational-andor-client-data-on-their-devices"></a>Role zaměstnanců a zodpovědnosti v procesu zálohování dat se identifikují a sdělují pracovníkům. Konkrétně je potřeba uvést si vlastní zařízení (BYOD), aby se na svých zařízeních prováděla zálohování podnikových a/nebo klientských dat.

**ID**: 1699.09 l1organizational. 10 – 09. l **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Azure Backup by měla být povolená Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Zajistěte ochranu Virtual Machines Azure tím, že povolíte Azure Backup. Azure Backup je bezpečné a nákladově efektivní řešení ochrany dat pro Azure. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="network-controls"></a>Ovládací prvky sítě

### <a name="the-organization-monitors-for-all-authorized-and-unauthorized-wireless-access-to-the-information-system-and-prohibits-installation-of-wireless-access-points-waps-unless-explicitly-authorized-in-writing-by-the-cio-or-hisher-designated-representative"></a>Organizace monitoruje veškerý autorizovaný a neautorizovaný bezdrátový přístup k informačnímu systému a zakazuje instalaci bezdrátových přístupových bodů (WAPs), pokud není explicitně autorizován CIO nebo jeho pověřený zástupce.

**ID**: 0858.09 m1organizational. 4 – 09. m **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Přístup přes internetový koncový bod by měl být omezený.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center identifikoval některé z příchozích pravidel vaší skupiny zabezpečení sítě tak, aby byly příliš opravňující. Příchozí pravidla by neměla umožňovat přístup z rozsahů "any" nebo "Internet". To může potenciálně umožnit útočníkům snadno cílit na vaše prostředky. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[Porty pro správu virtuálních počítačů by měly být chráněné pomocí řízení přístupu k síti za běhu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Přístup k síti je možný jenom v čase (JIT) se monitoruje Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Počítače s Windows by měly splňovat požadavky na Vlastnosti brány Windows Firewall.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35d9882c-993d-44e6-87d2-db66ce21b636) |Počítače s Windows by měly mít zadané nastavení Zásady skupiny v kategorii vlastnosti brány Windows Firewall pro stav brány firewall, připojení, správu pravidel a oznámení. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsFirewallProperties_AINE.json) |

### <a name="the-organization-ensures-the-security-of-information-in-networks-availability-of-network-services-and-information-services-using-the-network-and-the-protection-of-connected-services-from-unauthorized-access"></a>Organizace zajišťuje zabezpečení informací v sítích, dostupnosti síťových služeb a informačních služeb pomocí sítě a ochranu připojených služeb před neoprávněným přístupem.

**ID**: 0859.09 m1organizational. 78-09. m **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Doporučení adaptivního posílení zabezpečení sítě by se měla použít na internetových virtuálních počítačích.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analyzuje modely přenosů internetových virtuálních počítačů a poskytuje doporučení pro pravidla skupiny zabezpečení sítě, která omezují potenciální útok na plochu. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="the-organization-formally-manages-equipment-on-the-network-including-equipment-in-user-areas"></a>Organizace formálně spravuje zařízení v síti, včetně zařízení v oblastech uživatele.

**ID**: 0860.09 m1organizational. 9 – 09. m **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Nasadit nastavení diagnostiky pro skupiny zabezpečení sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9c29499-c1d1-4195-99bd-2ec9e3a9dc89) |Tato zásada automaticky nasadí nastavení diagnostiky do skupin zabezpečení sítě. Automaticky se vytvoří účet úložiště s názvem {storagePrefixParameter} {NSGLocation}. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForNSG_Deploy.json) |
|[Service Bus by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Tato zásada Audituje jakékoli Service Bus, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |

### <a name="to-identify-and-authenticate-devices-on-local-andor-wide-area-networks-including-wireless-networks-nbspthe-information-system-uses-either-a-i-shared-known-information-solution-or-ii-an-organizational-authentication-solution-the-exact-selection-and-strength-of-which-is-dependent-on-the-security-categorization-of-the-information-system"></a>Chcete-li identifikovat a ověřit zařízení v místních a/nebo sítích WAN, včetně bezdrátových sítí, &nbsp; používá informační systém buď sdílené známé informace o řešení, nebo (II) řešení ověřování organizace, přesný výběr a sílu, které jsou závislé na kategorizaci zabezpečení informačního systému.

**ID**: 0861.09 m2organizational. 67-09. m **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[App Service by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Tato zásada Audituje jakékoli App Service, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Počítače s Windows by měly splňovat požadavky na možnosti zabezpečení – síťový přístup.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ff60f98-7fa4-410a-9f7f-0b00f5afdbdd) |Počítače s Windows by měly mít zadaná nastavení Zásady skupiny v kategorii možnosti zabezpečení-síťový přístup, včetně přístupu anonymních uživatelů, místních účtů a vzdáleného přístupu k registru. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkAccess_AINE.json) |

### <a name="the-organization-ensures-information-systems-protect-the-confidentiality-and-integrity-of-transmitted-information-including-during-preparation-for-transmission-and-during-reception"></a>Organizace zajišťuje, aby informační systémy chránily důvěrnost a integritu přenesených informací, včetně během přípravy na přenos a při jejich příjmu.

**ID**: 0862.09 m2organizational. 8 – 09. m **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[SQL Server by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Tato zásada Audituje jakékoli SQL Server, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |

### <a name="the-organization-builds-a-firewall-configuration-that-restricts-connections-between-un-trusted-networks-and-any-system-components-in-the-covered-information-environment-and-any-changes-to-the-firewall-configuration-are-updated-in-the-network-diagram"></a>Organizace vytvoří konfiguraci brány firewall, která omezí připojení mezi nedůvěryhodnými sítěmi a všemi součástmi systému v zahrnutém informačním prostředí. a všechny změny konfigurace brány firewall se aktualizují v diagramu sítě.

**ID**: 0863.09 m2organizational. 910-09. m **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Centrum událostí by mělo používat koncový bod služby virtuální sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Tato zásada Audituje jakékoli centrum událostí, které není nakonfigurované na použití koncového bodu služby virtuální sítě. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |

### <a name="usage-restrictions-and-implementation-guidance-are-formally-defined-for-voip-including-the-authorization-and-monitoring-of-the-service"></a>Omezení využití a pokyny k implementaci jsou určené pro VoIP, včetně autorizací a monitorování služby.

**ID**: 0864.09 m2organizational. 12 – 09. m **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Cosmos DB by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Tato zásada Audituje jakékoli Cosmos DB, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

### <a name="the-organization-i-authorizes-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-through-the-use-of-interconnection-security-agreements-or-other-formal-agreement-ii-documents-each-connection-the-interface-characteristics-security-requirements-and-the-nature-of-the-information-communicated-iii-employs-a-deny-all-permit-by-exception-policy-for-allowing-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-and-iv-applies-a-default-deny-rule-that-drops-all-traffic-via-host-based-firewalls-or-port-filtering-tools-on-its-endpoints-workstations-servers-etc-except-those-services-and-ports-that-are-explicitly-allowed"></a>Organizace (i) autorizuje připojení z informačního systému k jiným informačním systémům mimo organizaci prostřednictvím smluv o zabezpečení propojení nebo jiné formální smlouvy. (II) dokumenty jednotlivých připojení, charakteristiky rozhraní, požadavky na zabezpečení a povaha sdělených informací; (III) využívá zamítnutí všech, povoluje zásady výjimek pro povolení připojení z informačního systému do jiných informačních systémů mimo organizaci. a (IV) aplikuje pravidlo s výchozím zamítnutím, které vyřazuje veškerý provoz přes brány firewall založené na hostiteli nebo nástroje pro filtrování portů na svých koncových bodech (pracovní stanice, servery atd.), s výjimkou těchto služeb a portů, které jsou výslovně povoleny.

**ID**: 0865.09 m2organizational. 13 – 09. m **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Key Vault by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Tato zásada Audituje jakékoli Key Vault, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |

### <a name="the-organization-describes-the-groups-roles-and-responsibilities-for-the-logical-management-of-network-components-and-ensures-coordination-of-and-consistency-in-the-elements-of-the-network-infrastructure"></a>Organizace popisuje skupiny, role a zodpovědnosti pro logickou správu síťových komponent a zajišťuje koordinaci a konzistenci prvků síťové infrastruktury.

**ID**: 0866.09 m3organizational. 1516-09. m **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Účty úložiště by měly omezovat síťový přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Přístup k síti účtů úložiště by měl být omezený. Nakonfigurujte Síťová pravidla, aby k účtu úložiště měly přístup jenom aplikace z povolených sítí. Aby bylo možné v připojeních z konkrétních internetových nebo místních klientů přistupovat k provozu z konkrétních virtuálních sítí Azure nebo do rozsahů veřejných IP adres sítě. |Audit, zamítnutí, zakázáno |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="wireless-access-points-are-placed-in-secure-areas-and-shut-down-when-not-in-use-eg-nights-weekends"></a>Bezdrátový přístupový bod je umístěn v zabezpečených oblastech a vypnutý, pokud se nepoužívá (například nocí, víkendy).

**ID**: 0867.09 m3organizational. 17-09. m **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Účty úložiště by měly používat koncový bod služby virtuální sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Tato zásada Audituje jakýkoliv účet úložiště, který není nakonfigurovaný tak, aby používal koncový bod služby virtuální sítě. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |

### <a name="the-organization-builds-a-firewall-configuration-to-restrict-inbound-and-outbound-traffic-to-that-which-is-necessary-for-the-covered-data-environment"></a>Organizace vytvoří konfiguraci brány firewall pro omezení příchozího a odchozího provozu, který je nezbytný pro zahrnuté datové prostředí.

**ID**: 0868.09 m3organizational. 18 – 09. m **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Container Registry by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Tato zásada Audituje jakékoli Container Registry, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |Audit, zakázáno |[1.0.0 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="the-router-configuration-files-are-secured-and-synchronized"></a>Konfigurační soubory směrovače jsou zabezpečené a synchronizované.

**ID**: 0869.09 m3organizational. 19 – 09. m **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Container Registry by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Tato zásada Audituje jakékoli Container Registry, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |Audit, zakázáno |[1.0.0 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="access-to-all-proxies-is-denied-except-for-those-hosts-ports-and-services-that-are-explicitly-required"></a>Přístup ke všem proxy je odepřený, s výjimkou těch hostitelů, portů a služeb, které jsou výslovně požadovány.

**ID**: 0870.09 m3organizational. 20-09. m **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Container Registry by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Tato zásada Audituje jakékoli Container Registry, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |Audit, zakázáno |[1.0.0 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="authoritative-dns-servers-are-segregated-into-internal-and-external-roles"></a>Autoritativní servery DNS jsou oddělené od interních a externích rolí.

**ID**: 0871.09 m3organizational. 22 – 09. m **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Container Registry by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Tato zásada Audituje jakékoli Container Registry, které nejsou nakonfigurované pro použití koncového bodu služby virtuální sítě. |Audit, zakázáno |[1.0.0 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

## <a name="security-of-network-services"></a>Zabezpečení Network Services

### <a name="agreed-services-provided-by-a-network-service-providermanager-are-formally-managed-and-monitored-to-ensure-they-are-provided-securely"></a>Dohodnuté služby, které poskytuje poskytovatel nebo správce síťové služby, jsou spravovány a monitorovány, aby byly zajištěny jejich bezpečné zabezpečení.

**ID**: 0835.09 n1organizational. 1-09. n **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Na virtuální počítače s Windows by měl být nainstalovaný Agent pro shromažďování dat pro síťové přenosy.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center používá agenta Microsoft Dependency Agent ke shromáždění dat síťových přenosů z virtuálních počítačů Azure, aby bylo možné pokročilé funkce ochrany sítě, jako je například vizualizace provozu na mapě sítě, doporučení pro posílení zabezpečení sítě a specifické hrozby sítě. |AuditIfNotExists, zakázáno |[1.0.1 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Virtuální počítače by se měly migrovat na nové prostředky Azure Resource Manager.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |Použití nových Azure Resource Manager pro virtuální počítače k zajištění vylepšení zabezpečení, jako je: silnější řízení přístupu (RBAC), lepší auditování, Azure Resource Manager nasazení a řízení přístupu, přístup ke spravovaným identitám, přístup k trezoru klíčů pro tajné klíče, ověřování a podpora na základě služby Azure AD pro značky a skupiny prostředků pro snadnější správu zabezpečení |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |

### <a name="the-organization-formally-authorizes-and-documents-the-characteristics-of-each-connection-from-an-information-system-to-other-information-systems-outside-the-organization"></a>Organizace formálně autorizuje a klade charakteristiky každého připojení z informačního systému do jiných informačních systémů mimo organizaci.

**ID**: 0836.09. n2Organizational. 1-09. n **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Agent shromažďování dat síťových přenosů by měl být nainstalovaný na virtuálních počítačích se systémem Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center používá agenta Microsoft Dependency Agent ke shromáždění dat síťových přenosů z virtuálních počítačů Azure, aby bylo možné pokročilé funkce ochrany sítě, jako je například vizualizace provozu na mapě sítě, doporučení pro posílení zabezpečení sítě a specifické hrozby sítě. |AuditIfNotExists, zakázáno |[1.0.1 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="formal-agreements-with-external-information-system-providers-include-specific-obligations-for-security-and-privacy"></a>Formální smlouvy s externími poskytovateli informačních systémů zahrnují zvláštní povinnosti pro zabezpečení a ochranu osobních údajů.

**ID**: 0837.09. n2Organizational. 2-09. n **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Network Watcher by měl být povolený.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher je místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni síťového scénáře v, do a z Azure. Monitorování úrovně scénáře umožňuje diagnostikovat problémy v zobrazení na úrovni koncových sítí. Nástroje pro diagnostiku a diagnostiku sítě, které jsou dostupné v Network Watcher, vám pomůžou pochopit, diagnostikovat a získávat přehledy vaší sítě v Azure. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-reviews-and-updates-the-interconnection-security-agreements-on-an-ongoing-basis-verifying-enforcement-of-security-requirements"></a>Organizace kontroluje a aktualizuje smlouvy zabezpečení propojení průběžně na základě ověření vynucování požadavků na zabezpečení.

**ID**: 0885.09 n2organizational. 3-09. n **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Agent shromažďování dat síťových přenosů by měl být nainstalovaný na virtuálních počítačích se systémem Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center používá agenta Microsoft Dependency Agent ke shromáždění dat síťových přenosů z virtuálních počítačů Azure, aby bylo možné pokročilé funkce ochrany sítě, jako je například vizualizace provozu na mapě sítě, doporučení pro posílení zabezpečení sítě a specifické hrozby sítě. |AuditIfNotExists, zakázáno |[1.0.1 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="the-organization-employs-and-documents-in-a-formal-agreement-or-other-document-either-i-allow-all-deny-by-exception-or-ii-deny-all-permit-by-exception-preferred-policy-for-allowing-specific-information-systems-to-connect-to-external-information-systems"></a>Organizace zaměstnává a dokumentuje v rámci formální smlouvy nebo jiného dokumentu, a to i) Allow-all, Deny-by-Exception nebo II) Deny-All, Permit-by-Exception (upřednostňovaná), zásady, které umožní konkrétním informačním systémům připojit se k externím informačním systémům.

**ID**: 0886.09 n2organizational. 4 – 09. n **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Network Watcher by měl být povolený.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher je místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni síťového scénáře v, do a z Azure. Monitorování úrovně scénáře umožňuje diagnostikovat problémy v zobrazení na úrovni koncových sítí. Nástroje pro diagnostiku a diagnostiku sítě, které jsou dostupné v Network Watcher, vám pomůžou pochopit, diagnostikovat a získávat přehledy vaší sítě v Azure. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-requires-externaloutsourced-service-providers-to-identify-the-specific-functions-ports-and-protocols-used-in-the-provision-of-the-externaloutsourced-services"></a>Organizace vyžaduje, aby poskytovatelé externích/dodavatelů služeb identifikovali konkrétní funkce, porty a protokoly používané při zřizování externích/nezdrojových služeb.

**ID**: 0887.09 n2organizational. 5-09. n **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Na virtuální počítače s Windows by měl být nainstalovaný Agent pro shromažďování dat pro síťové přenosy.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center používá agenta Microsoft Dependency Agent ke shromáždění dat síťových přenosů z virtuálních počítačů Azure, aby bylo možné pokročilé funkce ochrany sítě, jako je například vizualizace provozu na mapě sítě, doporučení pro posílení zabezpečení sítě a specifické hrozby sítě. |AuditIfNotExists, zakázáno |[1.0.1 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |

### <a name="the-contract-with-the-externaloutsourced-service-provider-includes-the-specification-that-the-service-provider-is-responsible-for-the-protection-of-covered-information-shared"></a>Smlouva s externím/nezdrojovým poskytovatelem služeb zahrnuje specifikaci, kterou poskytovatel služeb zodpovídá za ochranu zahrnutých informací, které jsou sdílené.

**ID**: 0888.09 n2organizational. 6 – 09. n **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Network Watcher by měl být povolený.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher je místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni síťového scénáře v, do a z Azure. Monitorování úrovně scénáře umožňuje diagnostikovat problémy v zobrazení na úrovni koncových sítí. Nástroje pro diagnostiku a diagnostiku sítě, které jsou dostupné v Network Watcher, vám pomůžou pochopit, diagnostikovat a získávat přehledy vaší sítě v Azure. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="management-of-removable-media"></a>Správa vyměnitelného média

### <a name="the-organization-based-on-the-data-classification-level-registers-media-including-laptops-prior-to-use-places-reasonable-restrictions-on-how-such-media-be-used-and-provides-an-appropriate-level-of-physical-and-logical-protection-including-encryption-for-media-containing-covered-information-until-properly-destroyed-or-sanitized"></a>Organizace na základě úrovně klasifikace dat registruje média (včetně přenosných počítačů) před použitím, omezuje přiměřená omezení způsobu použití těchto médií a poskytuje odpovídající úroveň fyzické a logické ochrany (včetně šifrování) pro média obsahující zahrnuté informace, dokud není správně zničena nebo upravena.

**ID**: 0301.09 o1organizational. 123-09. o **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Je třeba povolit transparentní šifrování dat databází SQL.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Transparentní šifrování dat by mělo být povoleno pro ochranu neaktivních dat a splnění požadavků na dodržování předpisů. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="the-organization-protects-and-controls-media-containing-sensitive-information-during-transport-outside-of-controlled-areas"></a>Organizace chrání a ovládá média obsahující citlivé údaje během přenosu mimo kontrolované oblasti.

**ID**: 0302.09 o2organizational. 1-09. o **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Na virtuálních počítačích by se mělo použít šifrování disku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Virtuální počítače bez povoleného šifrování disku se monitorují Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="digital-and-non-digital-media-requiring-restricted-use-and-the-specific-safeguards-used-to-restrict-their-use-are-identified"></a>Digitální a nedigitální média vyžadující omezené používání a určení specifických ochran používaných k omezení jejich používání.

**ID**: 0303.09 o2organizational. 2 – 09. o **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Nepřipojené disky by měly být zašifrované](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Tato zásada Audituje všechny nepřipojené disky bez povoleného šifrování. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="the-organization-restricts-the-use-of-writable-removable-media-and-personally-owned-removable-media-in-organizational-systems"></a>Organizace omezuje použití zapisovatelných vyměnitelných médií a osobních vyměnitelných médií v systémech organizace.

**ID**: 0304.09 o3organizational. 1-09. o **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Vyžadovat šifrování u Data Lake Store účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |Tato zásada zajistí, že se na všech účtech Data Lake Store povolí šifrování. |odmítnout |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |
|[Ochrana TDE spravované instance SQL by se měla šifrovat pomocí vlastního klíče.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Transparentní šifrování dat (TDE) s vaší vlastní klíčovou podporou přináší větší transparentnost a kontrolu nad ochranným prostředím TDE, zvýšené zabezpečení pomocí externí služby založené na HSM a povýšení oddělení povinností. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Ochrana TDE systému SQL Server by měla být zašifrovaná pomocí vlastního klíče](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Transparentní šifrování dat (TDE) s vaší vlastní klíčovou podporou přináší větší transparentnost a kontrolu nad ochranným prostředím TDE, zvýšené zabezpečení pomocí externí služby založené na HSM a povýšení oddělení povinností. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="information-exchange-policies-and-procedures"></a>Zásady a postupy výměny informací

### <a name="cloud-service-providers-use-an-industry-recognized-virtualization-platform-and-standard-virtualization-formats-eg-open-virtualization-format-ovf-to-help-ensure-interoperability-and-has-documented-custom-changes-made-to-any-hypervisor-in-use-and-all-solution-specific-virtualization-hooks-available-for-customer-review"></a>Poskytovatelé cloudových služeb používají pro zajištění interoperability rozpoznávanou virtualizační platformu a standardní virtualizační formáty (například Open Virtualization Format, OVF) a popsaly vlastní změny provedené v jakémkoli používaném hypervisoru a všechny moduly virtualizace pro konkrétní řešení, které jsou k dispozici pro kontrolu zákazníků.

**ID**: 0662.09 scsporganizational. 2 – 09. s **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Zajistěte, aby webová aplikace měla klientské certifikáty (příchozí klientské certifikáty) nastavené na zapnuto.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Klientské certifikáty umožňují, aby aplikace požádala o certifikát pro příchozí požadavky. Aplikaci budou moci kontaktovat pouze klienti s platným certifikátem. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-organization-formally-addresses-multiple-safeguards-before-allowing-the-use-of-information-systems-for-information-exchange"></a>Organizace si vyřeší několik ochranných opatření ještě předtím, než povolí použití informačních systémů pro výměnu informací.

**ID**: 0901.09 s1organizational. 1-09. s **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[CORS by neměl umožňovat každému prostředku přístup k vašim webovým aplikacím](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |Sdílení prostředků mezi zdroji (CORS) by nemělo umožňovat přístup k vaší webové aplikaci všem doménám. Povoluje interakci jenom požadovaných domén s webovou aplikací. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="remote-external-access-to-the-organizations-information-assets-and-access-to-external-information-assets-for-which-the-organization-has-no-control-is-based-on-clearly-defined-terms-and-conditions"></a>Vzdálený (externí) přístup k prostředkům organizace a přístup k externím informačním prostředkům (pro které organizace nemá žádný ovládací prvek) vychází z jasně definovaných podmínek a ujednání.

**ID**: 0902.09 s2organizational. 13-09. s **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[CORS by neměl umožňovat každému prostředku přístup k aplikacím funkcí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |Sdílení prostředků mezi zdroji (CORS) by nemělo umožňovat všem doménám přístup k vaší aplikaci Function App. Povoluje interakci jenom požadovaných domén s vaší aplikací Function App. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="the-organization-establishes-terms-and-conditions-consistent-with-any-trust-relationship-established-with-other-organizations-owning-operating-andor-maintaining-external-information-systems-allowing-authorized-individuals-to-i-access-the-information-system-from-external-information-systems-and-ii-process-store-or-transmit-organization-controlled-information-using-external-information-systems"></a>Organizace stanoví podmínky a ujednání, které jsou konzistentní s jakýmkoli vztahem důvěryhodnosti vytvořeným v jiných organizacích, které jsou v souladu s dalšími organizacemi, které vlastní, provozují nebo udržují externí informační systémy, a umožňují tak oprávněným jednotlivcům přístup k informačnímu systému z externích informačních systémů a (II) proces, ukládání nebo předávání informací řízených organizací pomocí externích informačních systémů.

**ID**: 0911.09 s1organizational. 2 – 09. s **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[CORS by neměl umožňovat každému prostředku přístup k vaší aplikaci API.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |Sdílení prostředků mezi zdroji (CORS) by nemělo umožňovat přístup k vaší aplikaci API všem doménám. Povoluje interakci jenom požadovaných domén s vaší aplikací API. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |

### <a name="cryptography-is-used-to-protect-the-confidentiality-and-integrity-of-remote-access-sessions-to-the-internal-network-and-to-external-systems"></a>Kryptografie se používá k ochraně důvěrnosti a integrity relací vzdáleného přístupu k interní síti a k externím systémům.

**ID**: 0912.09 s1organizational. 4 – 09. s **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Vzdálené ladění by mělo být pro webové aplikace vypnuté.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |Vzdálené ladění vyžaduje, aby byly na webové aplikaci otevřené porty pro příchozí spojení. Vzdálené ladění by mělo být vypnuté. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="strong-cryptography-protocols-are-used-to-safeguard-covered-information-during-transmission-over-less-trusted--open-public-networks"></a>Silné kryptografické protokoly slouží k ochraně zahrnutých informací během přenosu v méně důvěryhodných nebo otevřených veřejných sítích.

**ID**: 0913.09 s1organizational. 5-09. s **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro aplikace Function app by mělo být vypnuto vzdálené ladění.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Vzdálené ladění vyžaduje, aby byly pro aplikace Function App otevřené porty pro příchozí spojení. Vzdálené ladění by mělo být vypnuté. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-ensures-that-communication-protection-requirements-including-the-security-of-exchanges-of-information-is-the-subject-of-policy-development-and-compliance-audits"></a>Organizace zajišťuje, aby požadavky na komunikaci na ochranu, včetně zabezpečení výměny informací, byly předmětem vývoje zásad a auditů dodržování předpisů.

**ID**: 0914.09 s1organizational. 6 – 09. s **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Vzdálené ladění by mělo být pro API Apps vypnuté.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |Vzdálené ladění vyžaduje, aby byly pro aplikace API otevřené porty pro příchozí spojení. Vzdálené ladění by mělo být vypnuté. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-limits-the-use-of-organization-controlled-portable-storage-media-by-authorized-individuals-on-external-information-systems"></a>Organizace omezuje použití přenosného úložného média řízeného organizací autorizovanými osobami v externích informačních systémech.

**ID**: 0915.09 s2organizational. 2 – 09. s **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Zajistěte, aby webová aplikace měla klientské certifikáty (příchozí klientské certifikáty) nastavené na zapnuto.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Klientské certifikáty umožňují, aby aplikace požádala o certifikát pro příchozí požadavky. Aplikaci budou moci kontaktovat pouze klienti s platným certifikátem. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-information-system-prohibits-remote-activation-of-collaborative-computing-devices-and-provides-an-explicit-indication-of-use-to-users-physically-present-at-the-devices"></a>Informační systém zakazuje vzdálenou aktivaci spolupracujících výpočetních zařízení a poskytuje explicitní indikaci použití pro uživatele, kteří jsou v zařízeních fyzicky přítomni.

**ID**: 0916.09 s2organizational. 4-09. s **vlastnictvím**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[CORS by neměl umožňovat každému prostředku přístup k vašim webovým aplikacím](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |Sdílení prostředků mezi zdroji (CORS) by nemělo umožňovat přístup k vaší webové aplikaci všem doménám. Povoluje interakci jenom požadovaných domén s webovou aplikací. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="cloud-service-providers-use-secure-eg-non-clear-text-and-authenticated-standardized-network-protocols-for-the-import-and-export-of-data-and-to-manage-the-service-and-make-available-a-document-to-consumers-tenants-detailing-the-relevant-interoperability-and-portability-standards-that-are-involved"></a>Poskytovatelé cloudových služeb používají zabezpečené síťové protokoly (např. nejasný text a ověřované) pro účely importu a exportu dat a ke správě služby a zpřístupnění dokumentu příjemcům (klientům), které se týkají relevantních standardů vzájemné funkční spolupráce a přenositelnosti.

**ID**: 0960.09 scsporganizational. 1-09. s **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[CORS by neměl umožňovat každému prostředku přístup k aplikacím funkcí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |Sdílení prostředků mezi zdroji (CORS) by nemělo umožňovat všem doménám přístup k vaší aplikaci Function App. Povoluje interakci jenom požadovaných domén s vaší aplikací Function App. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="personnel-are-appropriately-trained-on-leading-principles-and-practices-for-all-types-of-information-exchange-oral-paper-and-electronic"></a>Zaměstnanci jsou vhodným způsobem vyškoleni na hlavních principech a postupech pro všechny typy výměn informací (orální, papírový a elektronický).

**ID**: 1325.09 s1organizational. 3 – 09. s **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro aplikace Function app by mělo být vypnuto vzdálené ladění.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Vzdálené ladění vyžaduje, aby byly pro aplikace Function App otevřené porty pro příchozí spojení. Vzdálené ladění by mělo být vypnuté. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

## <a name="on-line-transactions"></a>Transakce na řádku

### <a name="data-involved-in-electronic-commerce-and-online-transactions-is-checked-to-determine-if-it-contains-covered-information"></a>Data, která jsou součástí elektronického obchodování a online transakcí, se zkontrolují, aby se zjistilo, jestli obsahuje zahrnuté informace.

**ID**: 0943.09 y1organizational. 1-09. y **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Měl by se povolit zabezpečený přenos do účtů úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Audit požadavek na zabezpečený přenos v účtu úložiště. Zabezpečený přenos je možnost, která vynutí, aby váš účet úložiště přijímal požadavky jenom od zabezpečených připojení (HTTPS). Použití protokolu HTTPS zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako je třeba prostředník, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="protocols-used-to-communicate-between-all-involved-parties-are-secured-using-cryptographic-techniques-eg-ssl"></a>Protokoly používané pro komunikaci mezi všemi zúčastněnými stranami jsou zabezpečené pomocí kryptografických technik (např. SSL).

**ID**: 0945.09 y1organizational. 3-09. y **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Auditovat počítače s Windows, které neobsahují zadané certifikáty v důvěryhodném kořenovém adresáři](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F934345e1-4dfb-4c70-90d7-41990dc9608b) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud je úložiště důvěryhodných kořenových certifikátů počítače (CERT: \ LocalMachine\Root) neobsahuje minimálně jeden certifikát uvedený parametrem zásad. |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsCertificateInTrustedRoot_AINE.json) |

### <a name="the-organization-requires-the-use-of-encryption-between-and-the-use-of-electronic-signatures-by-each-of-the-parties-involved-in-the-transaction"></a>Organizace vyžaduje použití šifrování mezi a používání elektronických podpisů každou ze smluvních stran, které jsou zapojeny do transakce.

**ID**: 0946.09 y2organizational. 14-09. y **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditovat povolování jenom připojení přes SSL do Azure cache pro Redis. Použití zabezpečených připojení zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako jsou například prostředníky, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

### <a name="the-organization-ensures-the-storage-of-the-transaction-details-are-located-outside-of-any-publicly-accessible-environments-eg-on-a-storage-platform-existing-on-the-organizations-intranet-and-not-retained-and-exposed-on-a-storage-medium-directly-accessible-from-the-internet"></a>Organizace zajišťuje, že úložiště podrobností transakcí se nachází mimo všechna veřejně přístupná prostředí (například na platformě úložiště existující v intranetu organizace) a není zachovaná a vystavená na médiu úložiště, které je přímo přístupné z Internetu.

**ID**: 0947.09 y2organizational. 2-09. y **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro databázové servery PostgreSQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Tato zásada Audituje všechny PostgreSQL servery, které nevynucují připojení SSL. Azure Database for PostgreSQL upřednostňuje připojení klientských aplikací ke službě PostgreSQL pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž-in-the-middle" šifrováním datového proudu mezi serverem a vaší aplikací. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="where-a-trusted-authority-is-used-eg-for-the-purposes-of-issuing-and-maintaining-digital-signatures-andor-digital-certificates-security-is-integrated-and-embedded-throughout-the-entire-end-to-end-certificatesignature-management-process"></a>Pokud se používá důvěryhodná autorita (například pro účely vydávání a údržby digitálních podpisů a/nebo digitálních certifikátů), zabezpečení je integrováno a vloženo v celém kompletním procesu správy certifikátů a podpisů.

**ID**: 0948.09 y2organizational. 3-09. y **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Tato zásada Audituje všechny servery MySQL, které nevynucují připojení SSL. Azure Database for MySQL podporuje připojení Azure Database for MySQLho serveru k klientským aplikacím pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž" v prostředních prostředcích, a to šifrováním datového proudu mezi serverem a vaší aplikací. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-protocols-used-for-communications-are-enhanced-to-address-any-new-vulnerability-and-the-updated-versions-of-the-protocols-are-adopted-as-soon-as-possible"></a>Protokoly používané pro komunikaci jsou vylepšené tak, aby využívaly novou chybu zabezpečení a aktualizované verze protokolů byly přijímány co nejrychleji.

**ID**: 0949.09 y2organizational. 5 – 09. y **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Aplikace API by měla být přístupná jen přes protokol HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Function App by měl být přístupný jenom přes HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[V aplikaci API by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[V Function App by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Ve vaší webové aplikaci by se měla použít nejnovější verze TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Upgradovat na nejnovější verzi TLS |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Webová aplikace by měla být přístupná jen přes protokol HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="control-of-operational-software"></a>Řízení provozního softwaru

### <a name="only-authorized-administrators-are-allowed-to-implement-approved-upgrades-to-software-applications-and-program-libraries-based-on-business-requirements-and-the-security-implications-of-the-release"></a>Jenom autorizovaní správci můžou implementovat schválené upgrady na software, aplikace a knihovny programů, a to na základě obchodních požadavků a vlivu na zabezpečení verze.

**ID**: 0605.10 h1system. 12-10. h **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Servery, které nesplňují nakonfigurované směrné plány, se budou monitorovat Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Počítače s Windows by měly splňovat požadavky na možnosti zabezpečení – audit.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33936777-f2ac-45aa-82ec-07958ec9ade4) |Počítače s Windows by měly mít zadané nastavení Zásady skupiny v kategorii možnosti zabezpečení-audit pro vynucení podkategorie zásad auditu a vypnutí v případě, že není možné protokolovat audity zabezpečení. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAudit_AINE.json) |
|[Počítače s Windows by měly splňovat požadavky na zásady auditu systému – Správa účtů.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94d9aca8-3757-46df-aa51-f218c5f11954) |Počítače s Windows by měly mít zadané nastavení Zásady skupiny v kategorii "zásady auditu systému – Správa účtů" pro auditování aplikace, zabezpečení a správy skupin uživatelů a dalších událostí správy. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesAccountManagement_AINE.json) |

### <a name="applications-and-operating-systems-are-successfully-tested-for-usability-security-and-impact-prior-to-production"></a>Aplikace a operační systémy byly úspěšně testovány z hlediska použitelnosti, zabezpečení a dopadu před výrobou.

**ID**: 0606.10 h2system. 1-10. h **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by mělo být opraveno](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Auditujte ohrožení zabezpečení v konfiguraci zabezpečení na počítačích s nainstalovaným Docker a v Azure Security Center se zobrazují jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-uses-its-configuration-control-program-to-maintain-control-of-all-implemented-software-and-its-system-documentation-and-archive-prior-versions-of-implemented-software-and-associated-system-documentation"></a>Organizace používá svůj program pro řízení konfigurace k zachování kontroly nad veškerým implementovaným softwarem a jeho systémovou dokumentaci a k archivaci předchozích verzí implementovaného softwaru a příslušné systémové dokumentace.

**ID**: 0607.10 h2system. 23 – 10. h **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V počítačích by měly být povolené Adaptivní řízení aplikací pro definování bezpečných aplikací.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Povolit ovládací prvky aplikace, které definují seznam známých bezpečných aplikací spuštěných na vašich počítačích, a upozorní vás, když se spouštějí jiné aplikace. To pomáhá posílit zabezpečení vašich počítačů proti malwaru. Aby bylo možné zjednodušit proces konfigurace a údržby pravidel, Security Center využívá Machine Learning k analýze aplikací spuštěných na jednotlivých počítačích a k návrhu seznamu známých aplikací, které jsou v bezpečí. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Auditujte ohrožení zabezpečení operačního systému ve službě Virtual Machine Scale Sets, abyste je chránili před útoky. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

## <a name="change-control-procedures"></a>Změnit řídicí procedury

### <a name="managers-responsible-for-application-systems-are-also-responsible-for-the-strict-control-security-of-the-project-or-support-environment-and-ensure-that-all-proposed-system-changes-are-reviewed-to-check-that-they-do-not-compromise-the-security-of-either-the-system-or-the-operating-environment"></a>Manažeři zodpovědné za aplikační systémy jsou také odpovědni za striktní řízení (zabezpečení) projektu nebo prostředí podpory a zajišťují, aby byly zkontrolovány všechny navrhované změny systému, aby zkontrolovaly, že neohrožují zabezpečení systému nebo operačního prostředí.

**ID**: 0635.10 k1organizational. 12-10. k **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Počítače s Windows by měly splňovat požadavky na zásady auditu systému – podrobné sledování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Počítače s Windows by měly mít zadaná Zásady skupiny nastavení v kategorii zásady auditu systému – podrobné sledování pro audit DPAPI, vytváření a ukončování procesů, události RPC a aktivity PNP. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-formally-addresses-purpose-scope-roles-responsibilities-management-commitment-coordination-among-organizational-entities-and-compliance-for-configuration-management-eg-through-policies-standards-processes"></a>Organizace vychází z účelu, rozsahu, rolí, odpovědností, závazku na správu, koordinace mezi organizačními entitami a dodržování předpisů pro správu konfigurace (například prostřednictvím zásad, standardů, procesů).

**ID**: 0636.10 k2organizational. 1 – 10. k **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Počítače s Windows by měly splňovat požadavky na zásady auditu systému – podrobné sledování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Počítače s Windows by měly mít zadaná Zásady skupiny nastavení v kategorii zásady auditu systému – podrobné sledování pro audit DPAPI, vytváření a ukončování procesů, události RPC a aktivity PNP. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-has-developed-documented-and-implemented-a-configuration-management-plan-for-the-information-system"></a>Organizace vyvinula, zdokumentoval a implementovala plán správy konfigurace pro informační systém.

**ID**: 0637.10 k2organizational. 2 – 10. k **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Počítače s Windows by měly splňovat požadavky na zásady auditu systému – podrobné sledování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Počítače s Windows by měly mít zadaná Zásady skupiny nastavení v kategorii zásady auditu systému – podrobné sledování pro audit DPAPI, vytváření a ukončování procesů, události RPC a aktivity PNP. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="changes-are-formally-controlled-documented-and-enforced-in-order-to-minimize-the-corruption-of-information-systems"></a>Změny se formálně kontrolují, zdokumentují a vynutily, aby se minimalizovala poškození informačních systémů.

**ID**: 0638.10 k2organizational. 34569-10. k **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Počítače s Windows by měly splňovat požadavky na zásady auditu systému – podrobné sledování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Počítače s Windows by měly mít zadaná Zásady skupiny nastavení v kategorii zásady auditu systému – podrobné sledování pro audit DPAPI, vytváření a ukončování procesů, události RPC a aktivity PNP. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="installation-checklists-and-vulnerability-scans-are-used-to-validate-the-configuration-of-servers-workstations-devices-and-appliances-and-ensure-the-configuration-meets-minimum-standards"></a>Kontrolní seznamy pro instalaci a kontroly ohrožení zabezpečení se používají k ověření konfigurace serverů, pracovních stanic, zařízení a zařízení a zajištění, že konfigurace splňuje minimální standardy.

**ID**: 0639.10 k2organizational. 78-10. k **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Počítače s Windows by měly splňovat požadavky na zásady auditu systému – podrobné sledování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Počítače s Windows by měly mít zadaná Zásady skupiny nastavení v kategorii zásady auditu systému – podrobné sledování pro audit DPAPI, vytváření a ukončování procesů, události RPC a aktivity PNP. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="where-development-is-outsourced-change-control-procedures-to-address-security-are-included-in-the-contracts-and-specifically-require-the-developer-to-track-security-flaws-and-flaw-resolution-within-the-system-component-or-service-and-report-findings-to-organization-defined-personnel-or-roles"></a>V případě, že je vývoj ve zdroji, je třeba změnit kontrolní postupy na zabezpečení adres, které jsou zahrnuté ve kontraktech a konkrétně vyžadovat, aby vývojář sledoval chyby zabezpečení a řešení chyb v rámci systému, komponenty nebo služby a výsledků hlášení organizacím definovaným pracovníkům nebo rolím.

**ID**: 0640.10 k2organizational. 1012-10. k **vlastnictví**: nepoužitelné

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Počítače s Windows by měly splňovat požadavky na zásady auditu systému – podrobné sledování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Počítače s Windows by měly mít zadaná Zásady skupiny nastavení v kategorii zásady auditu systému – podrobné sledování pro audit DPAPI, vytváření a ukončování procesů, události RPC a aktivity PNP. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-does-not-use-automated-updates-on-critical-systems"></a>Organizace nepoužívá automatizované aktualizace pro důležité systémy.

**ID**: 0641.10 k2organizational. 11-10. k **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Počítače s Windows by měly splňovat požadavky na zásady auditu systému – podrobné sledování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Počítače s Windows by měly mít zadaná Zásady skupiny nastavení v kategorii zásady auditu systému – podrobné sledování pro audit DPAPI, vytváření a ukončování procesů, události RPC a aktivity PNP. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-develops-documents-and-maintains-under-configuration-control-a-current-baseline-configuration-of-the-information-system-and-reviews-and-updates-the-baseline-as-required"></a>Organizace vyvíjí, dokumentuje a udržuje v rámci správy konfigurace, aktuální základní konfiguraci informačního systému a kontroluje a aktualizuje směrný plán podle potřeby.

**ID**: 0642.10 k3organizational. 12-10. k **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Počítače s Windows by měly splňovat požadavky na zásady auditu systému – podrobné sledování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Počítače s Windows by měly mít zadaná Zásady skupiny nastavení v kategorii zásady auditu systému – podrobné sledování pro audit DPAPI, vytváření a ukončování procesů, události RPC a aktivity PNP. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-i-establishes-and-documents-mandatory-configuration-settings-for-information-technology-products-employed-within-the-information-system-using-the-latest-security-configuration-baselines-ii-identifies-documents-and-approves-exceptions-from-the-mandatory-established-configuration-settings-for-individual-components-based-on-explicit-operational-requirements-and-iii-monitors-and-controls-changes-to-the-configuration-settings-in-accordance-with-organizational-policies-and-procedures"></a>Organizace (i) zavádí a dokumentuje povinná nastavení konfigurace pro produkty informačních technologií používané v rámci informačního systému pomocí nejnovějších standardních hodnot konfigurace zabezpečení. (II) identifikuje, dokumentuje a schvaluje výjimky z povinných nastavení konfigurace pro jednotlivé komponenty na základě explicitních provozních požadavků. a (III) monitoruje a řídí změny nastavení konfigurace v souladu se zásadami a postupy organizace.

**ID**: 0643.10 k3organizational. 3-10. k **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Počítače s Windows by měly splňovat požadavky na zásady auditu systému – podrobné sledování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Počítače s Windows by měly mít zadaná Zásady skupiny nastavení v kategorii zásady auditu systému – podrobné sledování pro audit DPAPI, vytváření a ukončování procesů, události RPC a aktivity PNP. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-employs-automated-mechanisms-to-i-centrally-manage-apply-and-verify-configuration-settings-ii-respond-to-unauthorized-changes-to-network-and-system-security-related-configuration-settings-and-iii-enforce-access-restrictions-and-auditing-of-the-enforcement-actions"></a>Organizace používá automatizované mechanismy pro (i) centrálně spravovat, používat a ověřovat nastavení konfigurace. (II) reakce na neoprávněné změny nastavení konfigurace v souvislosti se zabezpečením sítě a systému; a (III) vynucují omezení přístupu a auditování akcí vynucení.

**ID**: 0644.10 k3organizational. 4 – 10. k **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Počítače s Windows by měly splňovat požadavky na zásady auditu systému – podrobné sledování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Počítače s Windows by měly mít zadaná Zásady skupiny nastavení v kategorii zásady auditu systému – podrobné sledování pro audit DPAPI, vytváření a ukončování procesů, události RPC a aktivity PNP. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

## <a name="control-of-technical-vulnerabilities"></a>Kontrola technických ohrožení zabezpečení

### <a name="technical-vulnerabilities-are-identified-evaluated-for-risk-and-corrected-in-a-timely-manner"></a>Jsou identifikovány technické chyby zabezpečení, vyhodnoceny pro rizika a včas opraveny.

**ID**: 0709.10 m1organizational. 1-10. m – **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Na virtuálních počítačích by mělo být povolené řešení posouzení ohrožení zabezpečení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Audituje virtuální počítače, aby zjistila, jestli jsou v něm podporované řešení pro posouzení ohrožení zabezpečení. Základní součástí každého internetového rizika a programu zabezpečení je identifikace a analýza ohrožení zabezpečení. Cenová úroveň Standard Azure Security Center zahrnuje kontrolu ohrožení zabezpečení pro vaše virtuální počítače bez dalších poplatků. Security Center taky může tento nástroj automaticky nasadit. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[Ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by mělo být opraveno](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Auditujte ohrožení zabezpečení v konfiguraci zabezpečení na počítačích s nainstalovaným Docker a v Azure Security Center se zobrazují jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Servery, které nesplňují nakonfigurované směrné plány, se budou monitorovat Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Auditujte ohrožení zabezpečení operačního systému ve službě Virtual Machine Scale Sets, abyste je chránili před útoky. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[Ohrožení zabezpečení vašich databází SQL by mělo být opraveno](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Sledujte výsledky kontroly ohrožení zabezpečení a doporučení pro napravení ohrožení zabezpečení databáze. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F760a85ff-6162-42b3-8d70-698e268f648c) |Monitoruje chyby zabezpečení zjištěné řešením posouzení ohrožení zabezpečení a virtuální počítače bez řešení posouzení ohrožení zabezpečení v Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |
|[Posouzení ohrožení zabezpečení by mělo být povoleno na spravované instanci SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Auditovat všechny spravované instance SQL, u kterých nejsou povolené kontroly opakovaného posuzování ohrožení zabezpečení Posouzení ohrožení zabezpečení může zjišťovat, sledovat a pomáhat při nápravě potenciálních ohrožení zabezpečení databáze. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[Na vašich serverech SQL by mělo být povolené posouzení ohrožení zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Auditovat servery Azure SQL, u kterých nejsou povolené kontroly opakovaného posuzování ohrožení zabezpečení Posouzení ohrožení zabezpečení může zjišťovat, sledovat a pomáhat při nápravě potenciálních ohrožení zabezpečení databáze. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
|[Počítače s Windows by měly splňovat požadavky na možnosti zabezpečení – síťový server Microsoftu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcaf2d518-f029-4f6b-833b-d7081702f253) |Počítače se systémem Windows by měly mít zadané nastavení Zásady skupiny v kategorii možnosti zabezpečení – server sítě Microsoft pro zakázání serveru SMB v1. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkServer_AINE.json) |

### <a name="a-hardened-configuration-standard-exists-for-all-system-and-network-components"></a>Pro všechny systémové a síťové součásti existuje zpřísněný standardní konfigurace.

**ID**: 0710.10 m2organizational. 1-10. m – **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Posouzení ohrožení zabezpečení by mělo být povoleno na spravované instanci SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Auditovat všechny spravované instance SQL, u kterých nejsou povolené kontroly opakovaného posuzování ohrožení zabezpečení Posouzení ohrožení zabezpečení může zjišťovat, sledovat a pomáhat při nápravě potenciálních ohrožení zabezpečení databáze. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

### <a name="a-technical-vulnerability-management-program-is-in-place-to-monitor-assess-rank-and-remediate-vulnerabilities-identified-in-systems"></a>Je k dismístě program pro správu technických ohrožení zabezpečení, který umožňuje monitorovat, vyhodnocovat, hodnotit a opravovat ohrožení zabezpečení identifikovanou v systémech.

**ID**: 0711.10 m2organizational. 23 – 10. m **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Na virtuálních počítačích by mělo být povolené řešení posouzení ohrožení zabezpečení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Audituje virtuální počítače, aby zjistila, jestli jsou v něm podporované řešení pro posouzení ohrožení zabezpečení. Základní součástí každého internetového rizika a programu zabezpečení je identifikace a analýza ohrožení zabezpečení. Cenová úroveň Standard Azure Security Center zahrnuje kontrolu ohrožení zabezpečení pro vaše virtuální počítače bez dalších poplatků. Security Center taky může tento nástroj automaticky nasadit. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="internal-and-external-vulnerability-assessments-of-covered-information-systems-virtualized-environments-and-networked-environments-including-both-network--and-application-layer-tests-are-performed-by-a-qualified-individual-on-a-quarterly-basis-or-after-significant-changes"></a>Interní a externí posouzení ohrožení zabezpečení zahrnutých informačních systémů, virtualizovaných prostředí a síťových prostředí, včetně testů síťových i aplikačních vrstev, provádí kvalifikovanou osobu na čtvrtletní bázi nebo po podstatných změnách.

**ID**: 0712.10 m2organizational. 4 – 10. m – **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F760a85ff-6162-42b3-8d70-698e268f648c) |Monitoruje chyby zabezpečení zjištěné řešením posouzení ohrožení zabezpečení a virtuální počítače bez řešení posouzení ohrožení zabezpečení v Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |

### <a name="patches-are-tested-and-evaluated-before-they-are-installed"></a>Opravy jsou před instalací testovány a vyhodnocovány.

**ID**: 0713.10 m2organizational. 5-10. m **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Servery, které nesplňují nakonfigurované směrné plány, se budou monitorovat Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-technical-vulnerability-management-program-is-evaluated-on-a-quarterly-basis"></a>Program pro správu technických ohrožení zabezpečení se vyhodnocuje čtvrtletní základně.

**ID**: 0714.10 m2organizational. 7-10. m **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Auditujte ohrožení zabezpečení operačního systému ve službě Virtual Machine Scale Sets, abyste je chránili před útoky. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="systems-are-appropriately-hardened-eg-configured-with-only-necessary-and-secure-services-ports-and-protocols-enabled"></a>Systémy jsou vhodně posílené (například konfigurované pouze s nezbytnými a zabezpečenými službami, porty a protokoly jsou povoleny).

**ID**: 0715.10 m2organizational. 8-10. m – **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by mělo být opraveno](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Auditujte ohrožení zabezpečení v konfiguraci zabezpečení na počítačích s nainstalovaným Docker a v Azure Security Center se zobrazují jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-conducts-an-enterprise-security-posture-review-as-needed-but-no-less-than-once-within-every-three-hundred-sixty-five-365-days-in-accordance-with-organizational-is-procedures"></a>Organizace provádí kontrolu podnikového zabezpečení stav podle potřeby, ale ne méně než jednou za každých 365 (365) dnů, v souladu s organizačními postupy.

**ID**: 0716.10 m3organizational. 1-10. m – **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Ohrožení zabezpečení vašich databází SQL by mělo být opraveno](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Sledujte výsledky kontroly ohrožení zabezpečení a doporučení pro napravení ohrožení zabezpečení databáze. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning-tools-include-the-capability-to-readily-update-the-information-system-vulnerabilities-scanned"></a>Mezi nástroje pro kontrolu ohrožení zabezpečení patří možnost okamžité aktualizace prověřených chyb zabezpečení informačního systému.

**ID**: 0717.10 m3organizational. 2 – 10. m **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Auditujte ohrožení zabezpečení operačního systému ve službě Virtual Machine Scale Sets, abyste je chránili před útoky. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="the-organization-scans-for-vulnerabilities-in-the-information-system-and-hosted-applications-to-determine-the-state-of-flaw-remediation-monthly-automatically-and-again-manually-or-automatically-when-new-vulnerabilities-potentially-affecting-the-systems-and-networked-environments-are-identified-and-reported"></a>Organizace vyhledá chyby zabezpečení v informačním systému a hostovaných aplikacích a určí stav nápravy chyby měsíčně (automaticky) a znovu (ručně nebo automaticky), když se identifikují a nahlásí nové chyby zabezpečení, které mohou ovlivnit systémy a síťová prostředí.

**ID**: 0718.10 m3organizational. 34-10. m – **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Servery, které nesplňují nakonfigurované směrné plány, se budou monitorovat Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-organization-updates-the-list-of-information-system-vulnerabilities-scanned-within-every-thirty-30-days-or-when-new-vulnerabilities-are-identified-and-reported"></a>Organizace aktualizuje seznam chyb zabezpečení informačního systému kontrolovaných během třiceti (30) dnů nebo při identifikaci a nahlášení nových chyb zabezpečení.

**ID**: 0719.10 m3organizational. 5-10. m **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Posouzení ohrožení zabezpečení by mělo být povoleno na spravované instanci SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Auditovat všechny spravované instance SQL, u kterých nejsou povolené kontroly opakovaného posuzování ohrožení zabezpečení Posouzení ohrožení zabezpečení může zjišťovat, sledovat a pomáhat při nápravě potenciálních ohrožení zabezpečení databáze. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

## <a name="business-continuity-and-risk-assessment"></a>Provozní kontinuita a hodnocení rizik

### <a name="the-organization-identifies-the-critical-business-processes-requiring-business-continuity"></a>Organizace identifikuje důležité obchodní procesy vyžadující kontinuitu podnikových procesů.

**ID**: 1634.12 b1organizational. 1 – 12. b **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Auditovat virtuální počítače bez nakonfigurovaného zotavení po havárii](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Auditujte virtuální počítače, u kterých není nakonfigurované zotavení po havárii. Další informace o zotavení po havárii najdete v [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

### <a name="information-security-aspects-of-business-continuity-are-i-based-on-identifying-events-or-sequence-of-events-that-can-cause-interruptions-to-the-organizations-critical-business-processes-eg-equipment-failure-human-errors-theft-fire-natural-disasters-acts-of-terrorism-ii-followed-by-a-risk-assessment-to-determine-the-probability-and-impact-of-such-interruptions-in-terms-of-time-damage-scale-and-recovery-period-iii-based-on-the-results-of-the-risk-assessment-a-business-continuity-strategy-is-developed-to-identify-the-overall-approach-to-business-continuity-and-iv-once-this-strategy-has-been-created-endorsement-is-provided-by-management-and-a-plan-created-and-endorsed-to-implement-this-strategy"></a>Aspekty zabezpečení informací o kontinuitě podnikových procesů jsou na základě identifikace událostí (nebo posloupnosti událostí), které můžou způsobit přerušení v důležitých obchodních procesech organizace (např. selhání zařízení, lidské chyby, krádež, požár, přírodní katastrofy terorismu); (II) následované posouzením rizik pro zjištění pravděpodobnosti a dopadu takových přerušení, a to v čase, škálování poškození a doba obnovení; (III) na základě výsledků posouzení rizik je vyvíjena strategie provozní kontinuity, která identifikuje celkový přístup k kontinuitě podnikových aplikací; a (IV) Po vytvoření této strategie je potvrzení poskytované správou a vytvoří se plán, který se vytvoří a schválí pro implementaci této strategie.

**ID**: 1635.12 b1organizational. 2 – 12. b **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Key Vault objekty by měly být obnovitelné](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Tato zásada Audituje, jestli nejsou objekty trezoru klíčů obnovitelné. Funkce obnovitelného odstranění pomáhá efektivně uchovávat prostředky pro danou dobu uchování (90 dní) i po operaci odstranění a zároveň dává dojem, že se objekt odstranil. Pokud je zapnutá možnost vyprázdnění ochrany, trezor nebo objekt v odstraněném stavu nelze odstranit, dokud neuplyne doba uchování 90 dnů. Tyto trezory a objekty můžou být pořád obnoveny, takže zákazníci, kteří budou dodržovat zásady uchovávání informací. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="business-impact-analysis-are-used-to-evaluate-the-consequences-of-disasters-security-failures-loss-of-service-and-service-availability"></a>Analýza dopadu na firmu slouží k vyhodnocení následků havárií, chyb zabezpečení, ztráty služby a dostupnosti služeb.

**ID**: 1637.12 b2organizational. 2 – 12. b **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Počítače s Windows by měly splňovat požadavky na možnosti zabezpečení v konzole pro zotavení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff71be03e-e25b-4d0f-b8bc-9b3e309b66c0) |Počítače s Windows by měly mít zadané nastavení Zásady skupiny v kategorii možnosti zabezpečení – konzola pro obnovení pro povolení kopírování a přístupu ke všem jednotkám a složkám. Tato zásada vyžaduje, aby byly požadavky na konfiguraci hostů nasazené do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsRecoveryconsole_AINE.json) |

### <a name="business-continuity-risk-assessments-i-are-carried-out-annually-with-full-involvement-from-owners-of-business-resources-and-processes-ii-consider-all-business-processes-and-is-not-limited-to-the-information-assets-but-includes-the-results-specific-to-information-security-and-iii-identifies-quantifies-and-prioritizes-risks-against-key-business-objectives-and-criteria-relevant-to-the-organization-including-critical-resources-impacts-of-disruptions-allowable-outage-times-and-recovery-priorities"></a>Posuzování rizik pro kontinuitu podnikových procesů (i) se provádí každoročně s plnou účastí od vlastníků obchodních prostředků a procesů. (II) berte v úvahu všechny obchodní procesy a není omezen na informační prostředky, ale obsahuje výsledky specifické pro zabezpečení informací. a (III) identifikují, kvantifikuje a upřednostní rizika pro klíčové obchodní cíle a kritéria týkající se organizace, včetně důležitých zdrojů, dopadů přerušení, povolených výpadků a priorit obnovení.

**ID**: 1638.12 b2organizational. 345 – 12. b **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Auditovat virtuální počítače bez nakonfigurovaného zotavení po havárii](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Auditujte virtuální počítače, u kterých není nakonfigurované zotavení po havárii. Další informace o zotavení po havárii najdete v [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

> [!NOTE]
> Dostupnost konkrétních definic Azure Policy se může v Azure Government a dalších národních cloudech lišit.

## <a name="next-steps"></a>Další kroky

Další články o Azure Policy:

- Přehled [dodržování legislativních předpisů](../concepts/regulatory-compliance.md) .
- Podívejte se na [strukturu definice iniciativy](../concepts/initiative-definition-structure.md).
- Podívejte se na další příklady na [Azure Policy Samples](./index.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
