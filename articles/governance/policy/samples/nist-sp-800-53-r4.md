---
title: Podrobnosti dodržování předpisů v souladu s předpisy pro NIST SP 800-53 R4
description: Podrobnosti o integrované iniciativě NIST SP 800-53 R4 dodržování předpisů Každý ovládací prvek je namapován na jednu nebo více Azure Policy definic, které pomáhají s posouzením.
ms.date: 09/16/2020
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: f26bd4276b8d0fc23fe015488e889b2fd13900f6
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90981702"
---
# <a name="details-of-the-nist-sp-800-53-r4-regulatory-compliance-built-in-initiative"></a>Podrobnosti o integrované iniciativě NIST SP 800-53 R4 dodržování předpisů

Následující článek podrobně popisuje, jak Azure Policyá definice předdefinovaného dodržování předpisů namapuje na domény a **ovládací prvky** **dodržování předpisů** v NIST SP 800-53 R4.
Další informace o tomto standardu dodržování předpisů najdete v tématu [NIST SP 800-53 R4](https://nvd.nist.gov/800-53). Pokud chcete pochopit _vlastnictví_, přečtěte si téma [definice zásad Azure Policy](../concepts/definition-structure.md#type) a [sdílená odpovědnost v cloudu](../../../security/fundamentals/shared-responsibility.md).

Následující mapování jsou k ovládacím prvkům **NIST SP 800-53 R4** . Pomocí navigace na pravé straně můžete přejít přímo ke konkrétní **doméně dodržování předpisů**. Mnohé z ovládacích prvků jsou implementovány s definicí [Azure Policy](../overview.md) iniciativou. Chcete-li zkontrolovat definici kompletní iniciativy, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** .
Pak vyhledejte a vyberte definici integrované iniciativy **NIST SP 800-53 R4** dodržování předpisů.

Tato integrovaná iniciativa se nasadí jako součást [ukázky NIST SP 800-53 R4](../../blueprints/samples/nist-sp-800-53-r4.md).

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../overview.md) .
> Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../how-to/get-compliance-data.md) pomocí ovládacího prvku. často však není 1:1 nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **se v Azure Policy** vztahuje pouze na samotné definice zásad; Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard kompatibility zahrnuje i ovládací prvky, které nejsou v tuto chvíli řešeny žádnými definicemi Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi doménami dodržování předpisů, ovládacími prvky a definicemi Azure Policy pro tento standard dodržování předpisů se mohou v průběhu času měnit. Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/NIST80053_audit.json).

## <a name="access-control"></a>Řízení přístupu

### <a name="access-control-policy-and-procedures"></a>Zásady Access Control a postupy

**ID**: NIST SP 800-53 R4 AC-1 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1000 – zásady a postupy pro Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ef3cc79-733e-48ed-ab6f-7bf439e9b406) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1000.json) |
|[Microsoft Managed Control 1001 – zásady a postupy pro Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e26f8c3-4bf3-4191-b8fc-d888805101b7) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1001.json) |

### <a name="account-management"></a>Správa účtů

**ID**: NIST SP 800-53 R4 AC-2 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Zastaralé účty by se měly odebírat z předplatného.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474) |Zastaralé účty by se měly odebírat z vašich předplatných.  Zastaralé účty jsou účty, kterým se zablokuje přihlášení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccounts_Audit.json) |
|[Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |Zastaralé účty s oprávněním vlastníka by měly být odebrány z vašeho předplatného.  Zastaralé účty jsou účty, kterým se zablokuje přihlášení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |
|[Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Aby nedocházelo k nemonitorovanému přístupu, měly by se z předplatného odebrat externí účty s oprávněním vlastníka. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Aby nedocházelo k nemonitorovanému přístupu, měly by se z předplatného odebrat externí účty s oprávněním ke čtení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Z vašeho předplatného byste měli odebrat externí účty s oprávněním k zápisu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |K tomu, aby se zabránilo nemonitorovanému přístupu, měly by se z předplatného odebrat externí účty s oprávněním k zápisu. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |
|[Spravovaný ovládací prvek Microsoft 1002 – Správa účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F632024c2-8079-439d-a7f6-90af1d78cc65) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1002.json) |
|[Spravovaný ovládací prvek Microsoft 1003 – Správa účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b68b179-3704-4ff7-b51d-7d65374d165d) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1003.json) |
|[Spravovaný ovládací prvek Microsoft 1004 – Správa účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc17822dc-736f-4eb4-a97d-e6be662ff835) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1004.json) |
|[Spravovaný ovládací prvek Microsoft 1005 – Správa účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b626abc-26d4-4e22-9de8-3831818526b1) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1005.json) |
|[Spravovaný ovládací prvek Microsoft 1006 – Správa účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faae8d54c-4bce-4c04-b3aa-5b65b67caac8) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1006.json) |
|[Spravovaný ovládací prvek Microsoft 1007 – Správa účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17200329-bf6c-46d8-ac6d-abf4641c2add) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1007.json) |
|[Spravovaný ovládací prvek Microsoft 1008 – Správa účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8356cfc6-507a-4d20-b818-08038011cd07) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1008.json) |
|[Spravovaný ovládací prvek Microsoft 1009 – Správa účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb26f8610-e615-47c2-abd6-c00b2b0b503a) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1009.json) |
|[Spravovaný ovládací prvek Microsoft 1010 – Správa účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F784663a8-1eb0-418a-a98c-24d19bc1bb62) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1010.json) |
|[Spravovaný ovládací prvek Microsoft 1011 – Správa účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7e6a54f3-883f-43d5-87c4-172dfd64a1f5) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1011.json) |
|[Spravovaný ovládací prvek Microsoft 1012 – Správa účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefd7b9ae-1db6-4eb6-b0fe-87e6565f9738) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1012.json) |

### <a name="account-management--automated-system-account-management"></a>Správa účtů | Automatizovaná správa účtů systému

**ID**: NIST SP 800-53 R4 AC-2 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1013-Account Management \| automatizovaná správa účtů systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fd7b917-d83b-4379-af60-51e14e316c61) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1013.json) |

### <a name="account-management--removal-of-temporary--emergency-accounts"></a>Správa účtů | Odebrání dočasných nebo nouzových účtů

**ID**: NIST SP 800-53 R4 AC-2 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1014 – Správa účtů – \| odebrání dočasných účtů/mimořádných účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5dee936c-8037-4df1-ab35-6635733da48c) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1014.json) |

### <a name="account-management--disable-inactive-accounts"></a>Správa účtů | Zakázat neaktivní účty

**ID**: NIST SP 800-53 R4 AC-2 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1015 – Správa účtů \| Zakázat neaktivní účty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F544a208a-9c3f-40bc-b1d1-d7e144495c14) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1015.json) |

### <a name="account-management--automated-audit-actions"></a>Správa účtů | Automatizované akce auditu

**ID**: NIST SP 800-53 R4 AC-2 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1016 – \| Akce automatizovaného auditu správy účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd8b43277-512e-40c3-ab00-14b3b6e72238) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1016.json) |

### <a name="account-management--inactivity-logout"></a>Správa účtů | Odhlášení nečinnosti

**ID**: NIST SP 800-53 R4 AC-2 (5) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1017 – \| odhlášení nečinnosti správy účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fc3db37-e59a-48c1-84e9-1780cedb409e) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1017.json) |

### <a name="account-management--role-based-schemes"></a>Správa účtů | Schémata založená na rolích

**ID**: NIST SP 800-53 R4 AC-2 (7) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro SQL servery by se měl zřídit správce Azure Active Directory.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Proveďte audit zřizování správce Azure Active Directory pro SQL Server, aby se povolilo ověřování Azure AD. Ověřování Azure AD umožňuje zjednodušenou správu oprávnění a centralizovanou správu identit uživatelů databáze a dalších služeb Microsoftu. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[Auditovat využití vlastních pravidel RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Místo vlastních rolí RBAC, které jsou náchylné k chybám, byste měli auditovat předdefinované role, jako je vlastník, přispěvatel, čtenář. Použití vlastních rolí se považuje za výjimku a vyžaduje přísné kontroly a modelování hrozeb. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Microsoft Managed Control 1018 – \| schémata založená na rolích správy účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9121abf-e698-4ee9-b1cf-71ee528ff07f) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1018.json) |
|[Microsoft Managed Control 1019 – \| schémata založená na rolích správy účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a3ee9b2-3977-459c-b8ce-2db583abd9f7) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1019.json) |
|[Microsoft Managed Control 1020 – \| schémata založená na rolích správy účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b291ee8-3140-4cad-beb7-568c077c78ce) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1020.json) |
|[Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Auditování využití ověřování klienta pouze prostřednictvím Azure Active Directory v Service Fabric |Audit, zamítnutí, zakázáno |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>Správa účtů | Omezení používání sdílených nebo skupinových účtů

**ID**: NIST SP 800-53 R4 AC-2 (9) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1021 – omezení správy účtů \| pro použití sdílených a skupinových účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a3eb0a3-428d-4669-baff-20a14eb4b551) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1021.json) |

### <a name="account-management--shared--group-account-credential-termination"></a>Správa účtů | Ukončení přihlašovacích údajů účtu Shared/Group

**ID**: NIST SP 800-53 R4 AC-2 (10) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1022 – \| ukončení přihlašovacích údajů účtu Shared/Group Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F411f7e2d-9a0b-4627-a0b9-1700432db47d) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1022.json) |

### <a name="account-management--usage-conditions"></a>Správa účtů | Podmínky použití

**ID**: NIST SP 800-53 R4 AC-2 (11) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravované řízení společnosti Microsoft 1023 – \| podmínky použití správy účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe55698b6-3dea-4aa9-99b9-d8218c6ab6e5) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1023.json) |

### <a name="account-management--account-monitoring--atypical-usage"></a>Správa účtů | Monitorování účtů/nezvyklé využití

**ID**: NIST SP 800-53 R4 AC-2 (12) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Porty pro správu virtuálních počítačů by měly být chráněné pomocí řízení přístupu k síti za běhu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Přístup k síti je možný jenom v čase (JIT) se monitoruje Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Microsoft Managed Control 1024 – \| monitorování/nezvyklé použití účtu správy účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84914fb4-12da-4c53-a341-a9fd463bed10) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1024.json) |
|[Microsoft Managed Control 1025 – \| monitorování/nezvyklé použití účtu správy účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fadfe020d-0a97-45f4-a39c-696ef99f3a95) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1025.json) |

### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>Správa účtů | Zakázání účtů pro vysoce rizikové jednotlivce

**ID**: NIST SP 800-53 R4 AC-2 (13) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1026 – Správa účtů: \| zákaz účtů pro vysoce rizikové jednotlivce](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55419419-c597-4cd4-b51e-009fd2266783) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1026.json) |

### <a name="access-enforcement"></a>Vynucování přístupu

**ID**: NIST SP 800-53 R4 AC-3 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1027 – vynucení přístupu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa76ca9b0-3f4a-4192-9a38-b25e4f8ae48c) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1027.json) |

### <a name="information-flow-enforcement"></a>Vynucování toku informací

**ID**: NIST SP 800-53 R4 AC-4 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[CORS by neměl umožňovat každému prostředku přístup k vašim webovým aplikacím](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |Sdílení prostředků mezi zdroji (CORS) by nemělo umožňovat přístup k vaší webové aplikaci všem doménám. Povoluje interakci jenom požadovaných domén s webovou aplikací. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |
|[Microsoft Managed Control 1028 – vynucení toku informací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff171df5c-921b-41e9-b12b-50801c315475) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1028.json) |

### <a name="information-flow-enforcement--security-policy-filters"></a>Vynucení toku informací | Filtry zásad zabezpečení

**ID**: NIST SP 800-53 R4 AC-4 (8) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1029 – \| filtry zásad zabezpečení vynucování toku informací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53ac8f8e-c2b5-4d44-8a2d-058e9ced9b69) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1029.json) |

### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>Vynucení toku informací | Fyzické a logické oddělení informačních toků

**ID**: NIST SP 800-53 R4 AC-4 (21) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1030 – \| fyzické a logické oddělení informačních toků k vynucení toku informací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd3531453-b869-4606-9122-29c1cd6e7ed1) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1030.json) |

### <a name="separation-of-duties"></a>Oddělení povinností

**ID**: NIST SP 800-53 R4 AC-5 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Doporučuje se určit až 3 vlastníky předplatného, aby se snížila pravděpodobnost porušení napadeného vlastníka. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[Audit počítačů s Windows chybějících zadaných členů ve skupině Administrators](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud místní skupina Administrators neobsahuje jednoho nebo více členů uvedených v parametru zásad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[Auditovat počítače s Windows, které mají zadané členy ve skupině Administrators](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud místní skupina Administrators obsahuje jednoho nebo více členů uvedených v parametru zásad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
|[Spravovaný ovládací prvek Microsoft 1031 – oddělení povinností](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b93a801-fe25-4574-a60d-cb22acffae00) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1031.json) |
|[Spravovaný ovládací prvek Microsoft 1032 – oddělení povinností](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5aa85661-d618-46b8-a20f-ca40a86f0751) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1032.json) |
|[Spravovaný ovládací prvek Microsoft 1033 – oddělení povinností](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48540f01-fc11-411a-b160-42807c68896e) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1033.json) |
|[K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Doporučuje se určit více než jednoho vlastníka předplatného, aby měl přístup správce k redundanci. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="least-privilege"></a>Nejnižší oprávnění

**ID**: NIST SP 800-53 R4 AC-6 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1034 – nejnižší oprávnění](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02a5ed00-6d2e-4e97-9a98-46c32c057329) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1034.json) |

### <a name="least-privilege--authorize-access-to-security-functions"></a>Nejnižší oprávnění | Autorizace přístupu k funkcím zabezpečení

**ID**: NIST SP 800-53 R4 AC-6 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1035 – minimální oprávnění \| k autorizaci přístupu k funkcím zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca94b046-45e2-444f-a862-dc8ce262a516) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1035.json) |

### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>Nejnižší oprávnění | Neprivilegovaný přístup pro funkce, které nepatří do zabezpečení

**ID**: NIST SP 800-53 R4 AC-6 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1036 – minimální oprávnění \| bez privilegovaného přístupu pro nezabezpečené funkce](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a16d673-8cf0-4dcf-b1d5-9b3e114fef71) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1036.json) |

### <a name="least-privilege--network-access-to-privileged-commands"></a>Nejnižší oprávnění | Síťový přístup k privilegovaným příkazům

**ID**: NIST SP 800-53 R4 AC-6 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1037 – nejnižší oprávnění \| síťový přístup k privilegovaným příkazům](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa4c2a3d-1294-41a3-9ada-0e540471e9fb) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1037.json) |

### <a name="least-privilege--privileged-accounts"></a>Nejnižší oprávnění | Privilegované účty

**ID**: NIST SP 800-53 R4 AC-6 (5) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1038 – privilegované účty s minimálním oprávněním \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26692e88-71b7-4a5f-a8ac-9f31dd05bd8e) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1038.json) |

### <a name="least-privilege--review-of-user-privileges"></a>Nejnižší oprávnění | Kontrola uživatelských oprávnění

**ID**: NIST SP 800-53 R4 AC-6 (7) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Doporučuje se určit až 3 vlastníky předplatného, aby se snížila pravděpodobnost porušení napadeného vlastníka. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[Audit počítačů s Windows chybějících zadaných členů ve skupině Administrators](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud místní skupina Administrators neobsahuje jednoho nebo více členů uvedených v parametru zásad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[Auditovat počítače s Windows, které mají zadané členy ve skupině Administrators](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud místní skupina Administrators obsahuje jednoho nebo více členů uvedených v parametru zásad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
|[Microsoft Managed Control 1039 – minimální \| Kontrola oprávnění uživatelů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a7b9de4-a8a2-4672-914d-c5f6752aa7f9) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1039.json) |
|[Microsoft Managed Control 1040 – minimální \| Kontrola oprávnění uživatelů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F54205576-cec9-463f-ba44-b4b3f5d0a84c) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1040.json) |
|[K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Doporučuje se určit více než jednoho vlastníka předplatného, aby měl přístup správce k redundanci. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="least-privilege--privilege-levels-for-code-execution"></a>Nejnižší oprávnění | Úrovně oprávnění pro provádění kódu

**ID**: NIST SP 800-53 R4 AC-6 (8) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1041 – minimální \| úrovně oprávnění pro provádění kódu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb3d8d15b-627a-4219-8c96-4d16f788888b) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1041.json) |

### <a name="least-privilege--auditing-use-of-privileged-functions"></a>Nejnižší oprávnění | Auditování používání privilegovaných funkcí

**ID**: NIST SP 800-53 R4 AC-6 (9) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1042 – auditování minimálních oprávnění \| pomocí privilegovaných funkcí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F319dc4f0-0fed-4ac9-8fc3-7aeddee82c07) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1042.json) |

### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>Nejnižší oprávnění | Zakázat uživatelům, kteří nemají oprávnění k provádění privilegovaných funkcí

**ID**: NIST SP 800-53 R4 AC-6 (10) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1043 – minimální oprávnění \| zakázat uživatelům, kteří nemají oprávnění k provádění privilegovaných funkcí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F361a77f6-0f9c-4748-8eec-bc13aaaa2455) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1043.json) |

### <a name="unsuccessful-logon-attempts"></a>Neúspěšné pokusy o přihlášení

**ID**: NIST SP 800-53 R4 AC-7 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1044 – neúspěšné pokusy o přihlášení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0abbac52-57cf-450d-8408-1208d0dd9e90) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1044.json) |
|[Microsoft Managed Control 1045 – neúspěšné pokusy o přihlášení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F554d2dd6-f3a8-4ad5-b66f-5ce23bd18892) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1045.json) |

### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>Neúspěšné pokusy o přihlášení | Vyprázdnit/vymazat mobilní zařízení

**ID**: NIST SP 800-53 R4 AC-7 (2) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1046 – automatické uzamčení účtu – \| mobilní zařízení vyprázdnění/vymazání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b1aa965-7502-41f9-92be-3e2fe7cc392a) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1046.json) |

### <a name="system-use-notification"></a>Oznámení o použití systému

**ID**: NIST SP 800-53 R4 AC-8 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1047 – oznámení o použití systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1ff6d62-a55c-41ab-90ba-90bb5b7b6f62) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1047.json) |
|[Microsoft Managed Control 1048 – oznámení o použití systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F483e7ca9-82b3-45a2-be97-b93163a0deb7) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1048.json) |
|[Microsoft Managed Control 1049 – oznámení o použití systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9adf7ba7-900a-4f35-8d57-9f34aafc405c) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1049.json) |

### <a name="concurrent-session-control"></a>Řízení souběžných relací

**ID**: NIST SP 800-53 R4 AC-10 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1050 – souběžné řízení relace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd20184c-b4ec-4ce5-8db6-6e86352d183f) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1050.json) |

### <a name="session-lock"></a>Zámek relace

**ID**: NIST SP 800-53 R4 AC-11 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1051 – zámek relace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7cac6ee9-b58b-40c8-a5ce-f0efc3d9b339) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1051.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1052 – zámek relace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F027cae1c-ec3e-4492-9036-4168d540c42a) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1052.json) |

### <a name="session-lock--pattern-hiding-displays"></a>Zámek relace | Vzor – skrytí displeje

**ID**: NIST SP 800-53 R4 AC-11 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1053 – vzor zámku relace \| – zobrazení skrývání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7582b19c-9dba-438e-aed8-ede59ac35ba3) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1053.json) |

### <a name="session-termination"></a>Ukončení relace

**ID**: NIST SP 800-53 R4 AC-12 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravované řízení od společnosti Microsoft 1054 – ukončení relace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5807e1b4-ba5e-4718-8689-a0ca05a191b2) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1054.json) |

### <a name="session-termination--user-initiated-logouts--message-displays"></a>Ukončení relace | Odhlášení iniciované uživatelem/zprávy

**ID**: NIST SP 800-53 R4 AC-12 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1055- \| odhlášení iniciované uživatelem a zprávami o ukončení relace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F769efd9b-3587-4e22-90ce-65ddcd5bd969) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1055.json) |
|[Microsoft Managed Control 1056- \| odhlášení iniciované uživatelem a zprávami o ukončení relace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac43352f-df83-4694-8738-cfce549fd08d) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1056.json) |

### <a name="permitted-actions-without-identification-or-authentication"></a>Povolené akce bez identifikace nebo ověřování

**ID**: NIST SP 800-53 R4 AC-14 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1057 – povolené akce bez identifikace nebo ověřování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F78255758-6d45-4bf0-a005-7016bc03b13c) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1057.json) |
|[Microsoft Managed Control 1058 – povolené akce bez identifikace nebo ověřování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F76e85d08-8fbb-4112-a1c1-93521e6a9254) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1058.json) |

### <a name="security-attributes"></a>Atributy zabezpečení

**ID**: NIST SP 800-53 R4 AC-16 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Auditujte všechny spravované instance SQL bez pokročilých zabezpečení dat. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Auditovat SQL servery bez pokročilých zabezpečení dat |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="remote-access"></a>Vzdálený přístup

**ID**: NIST SP 800-53 R4 AC-17 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1059 – vzdálený přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa29b5d9f-4953-4afe-b560-203a6410b6b4) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1059.json) |
|[Microsoft Managed Control 1060 – vzdálený přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34a987fd-2003-45de-a120-014956581f2b) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1060.json) |

### <a name="remote-access--automated-monitoring--control"></a>Vzdálený přístup | Automatizované monitorování a řízení

**ID**: NIST SP 800-53 R4 AC-17 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Přidat spravovanou identitu přiřazenou systémem a povolit přiřazení konfigurace hostů na virtuálních počítačích bez identit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |Tato zásada přidává spravovanou identitu přiřazenou systémem do virtuálních počítačů hostovaných v Azure, které jsou podporované konfigurací hosta, ale nemají žádné spravované identity. Spravovaná identita přiřazená systémem je předpokladem pro všechna přiřazení konfigurace hostů a musí se do počítačů přidat před použitím libovolných definic zásad konfigurace hostů. Další informace o konfiguraci hosta najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |upravíte |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[Přidat spravovanou identitu přiřazenou systémem a povolit přiřazení konfigurace hostů na virtuálních počítačích s identitou přiřazenou uživatelem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |Tato zásada přidává spravovanou identitu přiřazenou systémem do virtuálních počítačů hostovaných v Azure, které podporuje konfigurace hostů, a má alespoň jednu identitu přiřazenou uživatelem, ale nemá spravovanou identitu přiřazenou systémem. Spravovaná identita přiřazená systémem je předpokladem pro všechna přiřazení konfigurace hostů a musí se do počítačů přidat před použitím libovolných definic zásad konfigurace hostů. Další informace o konfiguraci hosta najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |upravíte |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[Auditovat počítače se systémem Linux, které umožňují vzdálená připojení z účtů bez hesla](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea53dbee-c6c9-4f0e-9f9e-de0039b78023) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud počítače se systémem Linux povolují vzdálená připojení z účtů bez hesel. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword110_AINE.json) |
|[Nasazení rozšíření konfigurace hosta pro Linux k povolení přiřazení konfigurace hostů na virtuálních počítačích se systémem Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |Tato zásada nasadí rozšíření konfigurace hosta pro Linux na virtuální počítače Linux hostované v Azure, které jsou podporované konfigurací hosta. Rozšíření konfigurace hosta pro Linux je předpokladem pro všechna přiřazení konfigurace hosta systému Linux a musí být nasazena do počítačů před použitím jakékoli definice zásad konfigurace hosta systému Linux. Další informace o konfiguraci hosta najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Nasazení rozšíření konfigurace hosta systému Windows pro povolení přiřazení konfigurace hostů na virtuálních počítačích s Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |Tato zásada nasadí rozšíření konfigurace hosta systému Windows na virtuální počítače s Windows hostované v Azure, které jsou podporované konfigurací hosta. Rozšíření konfigurace hosta systému Windows je předpokladem pro všechna přiřazení konfigurace hostů systému Windows a musí být nasazena do počítačů před použitím jakékoli definice zásad konfigurace služby Windows Host. Další informace o konfiguraci hosta najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Microsoft Managed Control 1061 – \| automatizované monitorování/řízení vzdáleného přístupu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ac22808-a2e8-41c4-9d46-429b50738914) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1061.json) |
|[Vzdálené ladění by mělo být pro API Apps vypnuté.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |Vzdálené ladění vyžaduje, aby byly pro aplikace API otevřené porty pro příchozí spojení. Vzdálené ladění by mělo být vypnuté. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[Pro aplikace Function app by mělo být vypnuto vzdálené ladění.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Vzdálené ladění vyžaduje, aby byly pro aplikace Function App otevřené porty pro příchozí spojení. Vzdálené ladění by mělo být vypnuté. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[Vzdálené ladění by mělo být pro webové aplikace vypnuté.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |Vzdálené ladění vyžaduje, aby byly na webové aplikaci otevřené porty pro příchozí spojení. Vzdálené ladění by mělo být vypnuté. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |
|[Účty úložiště by měly omezovat síťový přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Přístup k síti účtů úložiště by měl být omezený. Nakonfigurujte Síťová pravidla, aby k účtu úložiště měly přístup jenom aplikace z povolených sítí. Aby bylo možné v připojeních z konkrétních internetových nebo místních klientů přistupovat k provozu z konkrétních virtuálních sítí Azure nebo do rozsahů veřejných IP adres sítě. |Audit, zamítnutí, zakázáno |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>Vzdálený přístup | Ochrana důvěrnosti a integrity pomocí šifrování

**ID**: NIST SP 800-53 R4 AC-17 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1062 – \| ochrana důvěrnosti/integrity vzdáleného přístupu pomocí šifrování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4708723f-e099-4af1-bbf9-b6df7642e444) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1062.json) |

### <a name="remote-access--managed-access-control-points"></a>Vzdálený přístup | Spravované body Access Control

**ID**: NIST SP 800-53 R4 AC-17 (3) **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1063 – \| body Access Control spravované vzdáleným přístupem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F593ce201-54b2-4dd0-b34f-c308005d7780) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1063.json) |

### <a name="remote-access--privileged-commands--access"></a>Vzdálený přístup | Privilegované příkazy/přístup

**ID**: NIST SP 800-53 R4 AC-17 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1064 – \| privilegované příkazy a přístup pro vzdálený přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb4d9508-cbf0-4a3c-bb5c-6c95b159f3fb) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1064.json) |
|[Spravovaný ovládací prvek Microsoft 1065 – \| privilegované příkazy a přístup pro vzdálený přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff87b8085-dca9-4cf1-8f7b-9822b997797c) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1065.json) |

### <a name="remote-access--disconnect--disable-access"></a>Vzdálený přístup | Odpojit nebo zakázat přístup

**ID**: NIST SP 800-53 R4 AC-17 (9) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1066-vzdálený přístup \| odpojení/zakázání přístupu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4455c2e8-c65d-4acf-895e-304916f90b36) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1066.json) |

### <a name="wireless-access"></a>Bezdrátový přístup

**ID**: NIST SP 800-53 R4 AC-18 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1067 – bezdrátový přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c5e54f6-0127-44d0-8b61-f31dc8dd6190) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1067.json) |
|[Microsoft Managed Control 1068 – bezdrátový přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d045bca-a0fd-452e-9f41-4ec33769717c) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1068.json) |

### <a name="wireless-access--authentication-and-encryption"></a>Bezdrátový přístup | Ověřování a šifrování

**ID**: NIST SP 800-53 R4 AC-18 (1) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1069 – \| ověřování a šifrování bezdrátových přístupových kódů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F91c97b44-791e-46e9-bad7-ab7c4949edbb) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1069.json) |

### <a name="wireless-access--disable-wireless-networking"></a>Bezdrátový přístup | Zakázat bezdrátové sítě

**ID**: NIST SP 800-53 R4 AC-18 (3) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1070 – bezdrátový přístup \| zakáže bezdrátové sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68f837d0-8942-4b1e-9b31-be78b247bda8) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1070.json) |

### <a name="wireless-access--restrict-configurations-by-users"></a>Bezdrátový přístup | Omezení konfigurací podle uživatelů

**ID**: NIST SP 800-53 R4 AC-18 (4) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1071 – bezdrátový přístup \| omezuje konfigurace podle uživatelů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a437f5b-9ad6-4f28-8861-de404d511ae4) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1071.json) |

### <a name="wireless-access--antennas--transmission-power-levels"></a>Bezdrátový přístup | Antény/úrovně napájení přenosu

**ID**: NIST SP 800-53 R4 AC-18 (5) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1072 – antény bezdrátového přístupu \| /úrovně napájení přenosu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ca29e41-34ec-4e70-aba9-6248aca18c31) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1072.json) |

### <a name="access-control-for-mobile-devices"></a>Access Control pro mobilní zařízení

**ID**: NIST SP 800-53 R4 AC-19 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1073-Access Control pro mobilní zařízení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fab55cdb0-c7dd-4bd8-ae22-a7cea7594e9c) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1073.json) |
|[Microsoft Managed Control 1074-Access Control pro mobilní zařízení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F27a69937-af92-4198-9b86-08d355c7e59a) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1074.json) |

### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>Access Control pro mobilní zařízení | Úplné šifrování na základě zařízení/kontejneru

**ID**: NIST SP 800-53 R4 AC-19 (5) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1075-Access Control pro zařízení \| s plným šifrováním založeném na zařízení/kontejneru](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc933d22-04df-48ed-8f87-22a3773d4309) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1075.json) |

### <a name="use-of-external-information-systems"></a>Použití externích informačních systémů

**ID**: NIST SP 800-53 R4 AC-20 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1076 – použití externích informačních systémů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98a4bd5f-6436-46d4-ad00-930b5b1dfed4) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1076.json) |
|[Microsoft Managed Control 1077 – použití externích informačních systémů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2dad3668-797a-412e-a798-07d3849a7a79) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1077.json) |

### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>Používání externích informačních systémů | Omezení pro autorizované použití

**ID**: NIST SP 800-53 R4 AC-20 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1078 – použití omezení externích informačních systémů \| při autorizovaných použitích](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb25faf85-8a16-4f28-8e15-d05c0072d64d) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1078.json) |
|[Microsoft Managed Control 1079 – použití omezení externích informačních systémů \| při autorizovaných použitích](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F85c32733-7d23-4948-88da-058e2c56b60f) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1079.json) |

### <a name="use-of-external-information-systems--portable-storage-devices"></a>Používání externích informačních systémů | Přenosná úložná zařízení

**ID**: NIST SP 800-53 R4 AC-20 (2) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1080 – použití \| přenosného úložného zařízení s externími informačními systémy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F852981b4-a380-4704-aa1e-2e52d63445e5) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1080.json) |

### <a name="information-sharing"></a>Sdílení informací

**ID**: NIST SP 800-53 R4 AC-21 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1081 – sdílení informací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3867f2a9-23bb-4729-851f-c3ad98580caf) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1081.json) |
|[Microsoft Managed Control 1082 – sdílení informací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F24d480ef-11a0-4b1b-8e70-4e023bf2be23) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1082.json) |

### <a name="publicly-accessible-content"></a>Veřejně přístupný obsah

**ID**: NIST SP 800-53 R4 AC-22 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1083 – veřejně přístupný obsah](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e319cb6-2ca3-4a58-ad75-e67f484e50ec) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1083.json) |
|[Microsoft Managed Control 1084 – veřejně přístupný obsah](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0eb15db-dd1c-4d1d-b200-b12dd6cd060c) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1084.json) |
|[Microsoft Managed Control 1085 – veřejně přístupný obsah](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13d117e0-38b0-4bbb-aaab-563be5dd10ba) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1085.json) |
|[Microsoft Managed Control 1086 – veřejně přístupný obsah](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb321e6f-16a0-4be3-878f-500956e309c5) |Společnost Microsoft implementuje tento ovládací prvek Access Control |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1086.json) |

## <a name="awareness-and-training"></a>Povědomí a školení

### <a name="security-awareness-and-training-policy-and-procedures"></a>Zásady a postupy pro školení k zabezpečení

**ID**: NIST SP 800-53 R4 při 1 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1087 – zásady a postupy pro školení k zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F100c82ba-42e9-4d44-a2ba-94b209248583) |Společnost Microsoft implementuje toto povědomí a řízení školení. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1087.json) |
|[Microsoft Managed Control 1088 – zásady a postupy pro školení k zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d50f99d-1356-49c0-934a-45f742ba7783) |Společnost Microsoft implementuje toto povědomí a řízení školení. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1088.json) |

### <a name="security-awareness-training"></a>Školení k povědomí o zabezpečení

**ID**: NIST SP 800-53 R4 ve 2 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1089 – školení ke sledování bezpečnosti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef080e67-0d1a-4f76-a0c5-fb9b0358485e) |Společnost Microsoft implementuje toto povědomí a řízení školení. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1089.json) |
|[Microsoft Managed Control 1090 – školení ke sledování bezpečnosti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2fb740e5-cbc7-4d10-8686-d1bf826652b1) |Společnost Microsoft implementuje toto povědomí a řízení školení. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1090.json) |
|[Microsoft Managed Control 1091 – školení ke sledování bezpečnosti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb23bd715-5d1c-4e5c-9759-9cbdf79ded9d) |Společnost Microsoft implementuje toto povědomí a řízení školení. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1091.json) |

### <a name="security-awareness-training--insider-threat"></a>Školení pro povědomí o zabezpečení | Hrozba Insider

**ID**: NIST SP 800-53 R4 ve 2 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1092 – bezpečnostní povědomí školení \| Insider Threat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8a29d47b-8604-4667-84ef-90d203fcb305) |Společnost Microsoft implementuje toto povědomí a řízení školení. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1092.json) |

### <a name="role-based-security-training"></a>Školení zabezpečení na základě rolí

**ID**: NIST SP 800-53 R4 ve 3 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravované řízení společnosti Microsoft 1093 – školení zabezpečení na základě rolí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a0bdeeb-15f4-47e8-a1da-9f769f845fdf) |Společnost Microsoft implementuje toto povědomí a řízení školení. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1093.json) |
|[Spravované řízení společnosti Microsoft 1094 – školení zabezpečení na základě rolí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b1853e0-8973-446b-b567-09d901d31a09) |Společnost Microsoft implementuje toto povědomí a řízení školení. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1094.json) |
|[Spravované řízení společnosti Microsoft 1095 – školení zabezpečení na základě rolí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc3f6f7a-057b-433e-9834-e8c97b0194f6) |Společnost Microsoft implementuje toto povědomí a řízení školení. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1095.json) |

### <a name="role-based-security-training--practical-exercises"></a>Školení zabezpečení na základě rolí | Praktická cvičení

**ID**: NIST SP 800-53 R4 at-3 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravované řízení od Microsoftu 1096 – praktická cvičení pro zabezpečení na základě rolí \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F420c1477-aa43-49d0-bd7e-c4abdd9addff) |Společnost Microsoft implementuje toto povědomí a řízení školení. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1096.json) |

### <a name="role-based-security-training--suspicious-communications-and-anomalous-system-behavior"></a>Školení zabezpečení na základě rolí | Podezřelá komunikace a chování neobvyklé systému

**ID**: NIST SP 800-53 R4 ve 3 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravované řízení 1097 od Microsoftu – školení zabezpečení na základě rolí \| a chování neobvyklé systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf3e4836-f19e-47eb-a8cd-c3ca150452c0) |Společnost Microsoft implementuje toto povědomí a řízení školení. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1097.json) |

### <a name="security-training-records"></a>Záznamy školení zabezpečení

**ID**: NIST SP 800-53 R4 ve 4 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1098 – záznamy školení zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84363adb-dde3-411a-9fc1-36b56737f822) |Společnost Microsoft implementuje toto povědomí a řízení školení. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1098.json) |
|[Microsoft Managed Control 1099 – záznamy školení zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01910bab-8639-4bd0-84ef-cc53b24d79ba) |Společnost Microsoft implementuje toto povědomí a řízení školení. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1099.json) |

## <a name="audit-and-accountability"></a>Audit a zodpovědnost

### <a name="audit-and-accountability-policy-and-procedures"></a>Zásady a postupy pro audit a zodpovědnost

**ID**: NIST SP 800-53 R4 au-1 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1100 – zásady a postupy pro audit a zodpovědnost](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4057863c-ca7d-47eb-b1e0-503580cba8a4) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1100.json) |
|[Microsoft Managed Control 1101 – zásady a postupy pro audit a zodpovědnost](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7327b708-f0e0-457d-9d2a-527fcc9c9a65) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1101.json) |

### <a name="audit-events"></a>Události auditu

**ID**: NIST SP 800-53 R4 au-2 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1102 – události auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9943c16a-c54c-4b4a-ad28-bfd938cdbf57) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1102.json) |
|[Microsoft Managed Control 1103 – události auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16feeb31-6377-437e-bbab-d7f73911896d) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1103.json) |
|[Microsoft Managed Control 1104 – události auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcdd8d244-18b2-4306-a1d1-df175ae0935f) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1104.json) |
|[Microsoft Managed Control 1105 – události auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b73f57b-587d-4470-a344-0b0ae805f459) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1105.json) |

### <a name="audit-events--reviews-and-updates"></a>Události auditu | Recenze a aktualizace

**ID**: NIST SP 800-53 R4 au-2 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1106 – \| Revize a aktualizace událostí auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd2b4feae-61ab-423f-a4c5-0e38ac4464d8) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1106.json) |

### <a name="content-of-audit-records"></a>Obsah záznamů auditu

**ID**: NIST SP 800-53 R4 au-3 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1107-obsah záznamů auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb29ed931-8e21-4779-8458-27916122a904) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1107.json) |

### <a name="content-of-audit-records--additional-audit-information"></a>Obsah záznamů auditu | Další informace o auditu

**ID**: NIST SP 800-53 R4 au-3 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1108-obsah záznamů auditu \| Další informace o auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9ad559e-c12d-415e-9a78-e50fdd7da7ba) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1108.json) |

### <a name="content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Obsah záznamů auditu | Centralizovaná správa obsahu plánovaného záznamu auditu

**ID**: NIST SP 800-53 R4 au-3 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[\[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Hlásí virtuální počítače jako nevyhovující, pokud image virtuálního počítače (OS) není v seznamu definovaném a Agent není nainstalovaný. Seznam imagí operačního systému se v průběhu času aktualizuje, protože se podpora aktualizuje. |auditIfNotExists |[1.0.0 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[Audit Log Analyticsho nasazení agenta ve Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Nahlásí sadu škálování virtuálního počítače jako nedodržující předpisy, pokud se image virtuálního počítače (OS) nenachází v seznamu definovaném a Agent není nainstalovaný. Seznam imagí operačního systému se v průběhu času aktualizuje, protože se podpora aktualizuje. |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Audit Log Analytics pracovní prostor pro virtuální počítač – neshoda sestavy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Hlásí virtuální počítače jako nedodržující předpisy, pokud se neprotokolují do Log Analyticsho pracovního prostoru zadaného v přiřazení zásad nebo iniciativ. |ověřen |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[Microsoft Managed Control 1109-obsah auditů \| centralizované správy obsahu plánovaného záznamu auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d9ffa23-ad92-4d0d-b1f4-7db274cc2aec) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1109.json) |

### <a name="audit-storage-capacity"></a>Auditovat kapacitu úložiště

**ID**: NIST SP 800-53 R4 au-4 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1110 – kapacita úložiště auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6182bfa7-0f2a-43f5-834a-a2ddf31c13c7) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1110.json) |

### <a name="response-to-audit-processing-failures"></a>Reakce na selhání zpracování auditu

**ID**: NIST SP 800-53 R4 au-5 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Auditujte všechny spravované instance SQL bez pokročilých zabezpečení dat. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Auditovat SQL servery bez pokročilých zabezpečení dat |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Auditování nastavení diagnostiky](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |Auditování nastavení diagnostiky pro vybrané typy prostředků |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Auditování na SQL serveru by mělo být povolené.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Auditování v SQL Server by mělo být povoleno ke sledování databázových činností napříč všemi databázemi na serveru a jejich uložení v protokolu auditu. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Microsoft Managed Control 1111 – odpověď na selhání zpracování auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21de687c-f15e-4e51-bf8d-f35c8619965b) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1111.json) |
|[Microsoft Managed Control 1112 – odpověď na selhání zpracování auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd530aad8-4ee2-45f4-b234-c061dae683c0) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1112.json) |

### <a name="response-to-audit-processing-failures--audit-storage-capacity"></a>Odezva na selhání zpracování auditu | Auditovat kapacitu úložiště

**ID**: NIST SP 800-53 R4 au-5 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1113 – odpověď na selhání zpracování auditu auditovaná \| kapacita úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F562afd61-56be-4313-8fe4-b9564aa4ba7d) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1113.json) |

### <a name="response-to-audit-processing-failures--real-time-alerts"></a>Odezva na selhání zpracování auditu | Výstrahy v reálném čase

**ID**: NIST SP 800-53 R4 au-5 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1114 – odpověď na chyby zpracování auditu – \| výstrahy v reálném čase](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c090801-59bc-4454-bb33-e0455133486a) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1114.json) |

### <a name="audit-review-analysis-and-reporting"></a>Kontrola auditu, analýza a vytváření sestav

**ID**: NIST SP 800-53 R4 au-6 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1115 – kontrola auditu, analýza a vytváření sestav](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b653845-2ad9-4e09-a4f3-5a7c1d78353d) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1115.json) |
|[Microsoft Managed Control 1116 – kontrola auditu, analýza a vytváření sestav](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5e47bc51-35d1-44b8-92af-e2f2d8b67635) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1116.json) |

### <a name="audit-review-analysis-and-reporting--process-integration"></a>Kontrola auditu, analýza a vytváření sestav | Integrace procesu

**ID**: NIST SP 800-53 R4 au-6 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1117 – kontrola auditu, analýza a \| integrace procesu vytváření sestav](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fbfe680-6dbb-4037-963c-a621c5635902) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1117.json) |

### <a name="audit-review-analysis-and-reporting--correlate-audit-repositories"></a>Kontrola auditu, analýza a vytváření sestav | Korelace auditů úložiště

**ID**: NIST SP 800-53 R4 au-6 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1118 – kontrola auditu, analýza a vytváření sestav \| korelace auditu auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa96f743d-a195-420d-983a-08aa06bc441e) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1118.json) |

### <a name="audit-review-analysis-and-reporting--central-review-and-analysis"></a>Kontrola auditu, analýza a vytváření sestav | Centrální kontrola a analýza

**ID**: NIST SP 800-53 R4 au-6 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[\[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Hlásí virtuální počítače jako nevyhovující, pokud image virtuálního počítače (OS) není v seznamu definovaném a Agent není nainstalovaný. Seznam imagí operačního systému se v průběhu času aktualizuje, protože se podpora aktualizuje. |auditIfNotExists |[1.0.0 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[Audit Log Analyticsho nasazení agenta ve Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Nahlásí sadu škálování virtuálního počítače jako nedodržující předpisy, pokud se image virtuálního počítače (OS) nenachází v seznamu definovaném a Agent není nainstalovaný. Seznam imagí operačního systému se v průběhu času aktualizuje, protože se podpora aktualizuje. |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Audit Log Analytics pracovní prostor pro virtuální počítač – neshoda sestavy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Hlásí virtuální počítače jako nedodržující předpisy, pokud se neprotokolují do Log Analyticsho pracovního prostoru zadaného v přiřazení zásad nebo iniciativ. |ověřen |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[Spravovaný ovládací prvek Microsoft 1119 – kontrola auditu, analýza a \| centrální přezkoumání a analýza sestav](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F845f6359-b764-4b40-b579-657aefe23c44) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1119.json) |

### <a name="audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>Kontrola auditu, analýza a vytváření sestav | Možnosti integrace, kontroly a monitorování

**ID**: NIST SP 800-53 R4 au-6 (5) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1120 – revize auditu, analýza a vytváření sestav, \| Možnosti kontroly a monitorování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc69b870e-857b-458b-af02-bb234f7a00d3) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1120.json) |

### <a name="audit-review-analysis-and-reporting--correlation-with-physical-monitoring"></a>Kontrola auditu, analýza a vytváření sestav | Korelace s fyzickým monitorováním

**ID**: NIST SP 800-53 R4 au-6 (6) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1121 – korelace kontroly auditu, analýzy a vytváření sestav \| s fyzickým monitorováním](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc72b0eb9-1fc2-44e5-a866-e7cb0532f7c1) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1121.json) |

### <a name="audit-review-analysis-and-reporting--permitted-actions"></a>Kontrola auditu, analýza a vytváření sestav | Povolené akce

**ID**: NIST SP 800-53 R4 au-6 (7) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1122 – kontrolní opatření pro kontrolu, analýzu a generování sestav \| povolených](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F243ec95e-800c-49d4-ba52-1fdd9f6b8b57) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1122.json) |

### <a name="audit-review-analysis-and-reporting--audit-level-adjustment"></a>Kontrola auditu, analýza a vytváření sestav | Úprava úrovně auditu

**ID**: NIST SP 800-53 R4 au-6 (10) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1123 – úprava auditu, analýza a \| Nastavení úrovně auditu pro vytváření sestav](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03996055-37a4-45a5-8b70-3f1caa45f87d) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1123.json) |

### <a name="audit-reduction-and-report-generation"></a>Omezení auditu a generování sestav

**ID**: NIST SP 800-53 R4 au-7 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1124 – omezení auditu a generování sestav](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc10152dd-78f8-4335-ae2d-ad92cc028da4) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1124.json) |
|[Spravovaný ovládací prvek Microsoft 1125 – omezení auditu a generování sestav](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc6ce745a-670e-47d3-a6c4-3cfe5ef00c10) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1125.json) |

### <a name="audit-reduction-and-report-generation--automatic-processing"></a>Omezení auditu a generování sestav | Automatické zpracování

**ID**: NIST SP 800-53 R4 au-7 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1126 – snížení auditu a \| Automatické zpracování generování sestav](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f37f71b-420f-49bf-9477-9c0196974ecf) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1126.json) |

### <a name="time-stamps"></a>Časová razítka

**ID**: NIST SP 800-53 R4 au-8 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1127 – časová razítka](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ce328db-aef3-48ed-9f81-2ab7cf839c66) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1127.json) |
|[Microsoft Managed Control 1128 – časová razítka](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef212163-3bc4-4e86-bcf8-705127086393) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1128.json) |

### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Časová razítka | Synchronizace s autoritativním zdrojem času

**ID**: NIST SP 800-53 R4 au-8 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control Control 1129 – synchronizace časových razítek \| s autoritativním zdrojem času](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71bb965d-4047-4623-afd4-b8189a58df5d) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1129.json) |
|[Microsoft Managed Control Control 1130 – synchronizace časových razítek \| s autoritativním zdrojem času](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd7c4c1d-51ee-4349-9dab-89a7f8c8d102) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1130.json) |

### <a name="protection-of-audit-information"></a>Ochrana informací o auditu

**ID**: NIST SP 800-53 R4 au-9 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1131 – ochrana informací o auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb472a17e-c2bc-493f-b50b-42d55a346962) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1131.json) |

### <a name="protection-of-audit-information--audit-backup-on-separate-physical-systems--components"></a>Ochrana informací o auditu | Auditovat zálohu v samostatných fyzických systémech/součástech

**ID**: NIST SP 800-53 R4 au-9 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1132 – ochrana \| auditu audit Information audit v samostatných fyzických systémech/součástech](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05938e10-cdbd-4a54-9b2b-1cbcfc141ad0) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1132.json) |

### <a name="protection-of-audit-information--cryptographic-protection"></a>Ochrana informací o auditu | Kryptografická ochrana

**ID**: NIST SP 800-53 R4 au-9 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1133 – ochrana \| kryptografické ochrany informací auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90b60a09-133d-45bc-86ef-b206a6134bbe) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1133.json) |

### <a name="protection-of-audit-information--access-by-subset-of-privileged-users"></a>Ochrana informací o auditu | Přístup podle podmnožiny privilegovaných uživatelů

**ID**: NIST SP 800-53 R4 au-9 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1134 – Ochrana přístupu k informacím o auditu \| pomocí podmnožiny privilegovaných uživatelů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e95f70e-181c-4422-9da2-43079710c789) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1134.json) |

### <a name="non-repudiation"></a>Neodvolatelnost

**ID**: NIST SP 800-53 R4 au-10 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1135 – Neodmítnutí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9c308b6b-2429-4b97-86cf-081b8e737b04) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1135.json) |

### <a name="audit-record-retention"></a>Uchovávání záznamů auditu

**ID**: NIST SP 800-53 R4 au-11 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1136-uchovávání záznamů auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F97ed5bac-a92f-4f6d-a8ed-dc094723597c) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1136.json) |

### <a name="audit-generation"></a>Generování auditu

**ID**: NIST SP 800-53 R4 au-12 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[\[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Hlásí virtuální počítače jako nevyhovující, pokud image virtuálního počítače (OS) není v seznamu definovaném a Agent není nainstalovaný. Seznam imagí operačního systému se v průběhu času aktualizuje, protože se podpora aktualizuje. |auditIfNotExists |[1.0.0 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Auditujte všechny spravované instance SQL bez pokročilých zabezpečení dat. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Auditovat SQL servery bez pokročilých zabezpečení dat |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Auditování nastavení diagnostiky](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |Auditování nastavení diagnostiky pro vybrané typy prostředků |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Audit Log Analyticsho nasazení agenta ve Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Nahlásí sadu škálování virtuálního počítače jako nedodržující předpisy, pokud se image virtuálního počítače (OS) nenachází v seznamu definovaném a Agent není nainstalovaný. Seznam imagí operačního systému se v průběhu času aktualizuje, protože se podpora aktualizuje. |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Audit Log Analytics pracovní prostor pro virtuální počítač – neshoda sestavy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Hlásí virtuální počítače jako nedodržující předpisy, pokud se neprotokolují do Log Analyticsho pracovního prostoru zadaného v přiřazení zásad nebo iniciativ. |ověřen |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[Auditování na SQL serveru by mělo být povolené.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Auditování v SQL Server by mělo být povoleno ke sledování databázových činností napříč všemi databázemi na serveru a jejich uložení v protokolu auditu. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Spravovaný ovládací prvek Microsoft 1137 – generování auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4344df62-88ab-4637-b97b-bcaf2ec97e7c) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1137.json) |
|[Spravovaný ovládací prvek Microsoft 1138 – generování auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9c284fc0-268a-4f29-af44-3c126674edb4) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1138.json) |
|[Spravovaný ovládací prvek Microsoft 1139 – generování auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ed62522-de00-4dda-9810-5205733d2f34) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1139.json) |

### <a name="audit-generation--system-wide--time-correlated-audit-trail"></a>Generování auditu | Záznam auditu v reálném čase a podle časového rozsahu

**ID**: NIST SP 800-53 R4 au-12 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1140 – \| záznam auditu v systému v reálném čase a podle časového rozsahu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90d8b8ad-8ee3-4db7-913f-2a53fcff5316) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1140.json) |

### <a name="audit-generation--changes-by-authorized-individuals"></a>Generování auditu | Změny prováděné autorizovanými osobami

**ID**: NIST SP 800-53 R4 au-12 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1141 – změny pro generování auditu \| povolují Jednotlivci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fdefbf4-93e7-4513-bc95-c1858b7093e0) |Microsoft implementuje tento ovládací prvek pro audit a zodpovědnost. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1141.json) |

## <a name="security-assessment-and-authorization"></a>Posouzení zabezpečení a autorizace

### <a name="security-assessment-and-authorization-policy-and-procedures"></a>Postupy a zásady ověřování zabezpečení

**ID**: NIST SP 800-53 R4 ca-1 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1142 – zásady a zásady autorizace zabezpečení a postupy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01524fa8-4555-48ce-ba5f-c3b8dcef5147) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1142.json) |
|[Microsoft Managed Control 1143 – zásady a zásady autorizace zabezpečení a postupy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c6de11b-5f51-4f7c-8d83-d2467c8a816e) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1143.json) |

### <a name="security-assessments"></a>Posouzení zabezpečení

**ID**: NIST SP 800-53 R4 CA-2 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1144 – posouzení zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2fa15ff1-a693-4ee4-b094-324818dc9a51) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1144.json) |
|[Microsoft Managed Control 1145 – posouzení zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0724970-9c75-4a64-a225-a28002953f28) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1145.json) |
|[Microsoft Managed Control 1146 – posouzení zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd83410c-ecb6-4547-8f14-748c3cbdc7ac) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1146.json) |
|[Microsoft Managed Control 1147 – posouzení zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fef824a-29a8-4a4c-88fc-420a39c0d541) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1147.json) |

### <a name="security-assessments--independent-assessors"></a>Posouzení zabezpečení | Nezávislí hodnotitelé

**ID**: NIST SP 800-53 R4 CA-2 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1148 – posouzení zabezpečení \| nezávislá posouzení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28e62650-c7c2-4786-bdfa-17edc1673902) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1148.json) |

### <a name="security-assessments--specialized-assessments"></a>Posouzení zabezpečení | Specializovaná posouzení

**ID**: NIST SP 800-53 R4 CA-2 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1149-posouzení zabezpečení – \| specializované posouzení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e1b855b-a013-481a-aeeb-2bcb129fd35d) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1149.json) |

### <a name="security-assessments--external-organizations"></a>Posouzení zabezpečení | Externí organizace

**ID**: NIST SP 800-53 R4 CA-2 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1150 – posouzení zabezpečení \| externích organizací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd630429d-e763-40b1-8fba-d20ba7314afb) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1150.json) |

### <a name="system-interconnections"></a>Propojení systému

**ID**: NIST SP 800-53 R4 CA-3 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1151 – propojení systémů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F347e3b69-7fb7-47df-a8ef-71a1a7b44bca) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1151.json) |
|[Microsoft Managed Control 1152 – propojení systémů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbeff0acf-7e67-40b2-b1ca-1a0e8205cf1b) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1152.json) |
|[Microsoft Managed Control 1153 – propojení systémů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F61cf3125-142c-4754-8a16-41ab4d529635) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1153.json) |

### <a name="system-interconnections--unclassified-non-national-security-system-connections"></a>Propojení systému | Neklasifikovaná neklasifikovaná nenárodní připojení systému zabezpečení

**ID**: NIST SP 800-53 R4 CA-3 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1154-systémy \| propojovat neklasifikovaná nenárodní připojení systému zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe757ceb9-93b3-45fe-a4f4-f43f64f1ac5a) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1154.json) |

### <a name="system-interconnections--restrictions-on-external-system-connections"></a>Propojení systému | Omezení pro připojení externích systémů

**ID**: NIST SP 800-53 R4 CA-3 (5) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1155 – \| omezení pro připojení k externím systémům v systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d33f9f1-12d0-46ad-9fbd-8f8046694977) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1155.json) |

### <a name="plan-of-action-and-milestones"></a>Plán akcí a milníků

**ID**: NIST SP 800-53 R4 CA-5 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1156 – plán akcí a milníků](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d52e864-9a3b-41ee-8f03-520815fe5378) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1156.json) |
|[Microsoft Managed Control 1157 – plán akcí a milníků](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F15495367-cf68-464c-bbc3-f53ca5227b7a) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1157.json) |

### <a name="security-authorization"></a>Autorizace zabezpečení

**ID**: NIST SP 800-53 R4 ca-6 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1158 – autorizace zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffff50cf2-28eb-45b4-b378-c99412688907) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1158.json) |
|[Microsoft Managed Control 1159 – autorizace zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0925f098-7877-450b-8ba4-d1e55f2d8795) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1159.json) |
|[Microsoft Managed Control 1160 – autorizace zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3e797ca6-2aa8-4333-b335-7036f1110c05) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1160.json) |

### <a name="continuous-monitoring"></a>Průběžné monitorování

**ID**: NIST SP 800-53 R4 CA-7 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1161 – nepřetržité monitorování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2f8f6c6-dde4-436b-a79d-bc50e129eb3a) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1161.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1162 – nepřetržité monitorování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5770f3d6-8c2b-4f6f-bf0e-c8c8fc36d592) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1162.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1163 – nepřetržité monitorování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F961663a1-8a91-4e59-b6f5-1eee57c0f49c) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1163.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1164 – nepřetržité monitorování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fb8d3ce-9e96-481c-9c68-88d4e3019310) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1164.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1165 – nepřetržité monitorování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47e10916-6c9e-446b-b0bd-ff5fd439d79d) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1165.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1166 – nepřetržité monitorování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb02733d-3cc5-4bb0-a6cd-695ba2c2272e) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1166.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1167 – nepřetržité monitorování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcbb2be76-4891-430b-95a7-ca0b0a3d1300) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1167.json) |

### <a name="continuous-monitoring--independent-assessment"></a>Průběžné monitorování | Nezávislé posouzení

**ID**: NIST SP 800-53 R4 CA-7 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1168 – \| nezávislá kontrola nezávislého monitorování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82409f9e-1f32-4775-bf07-b99d53a91b06) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1168.json) |

### <a name="continuous-monitoring--trend-analyses"></a>Průběžné monitorování | Analýzy trendů

**ID**: NIST SP 800-53 R4 CA-7 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1169 – \| analýzy trendů nepřetržitého monitorování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe7ba2cb3-5675-4468-8b50-8486bdd998a5) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1169.json) |

### <a name="penetration-testing"></a>Testování průniku

**ID**: NIST SP 800-53 R4 CA-8 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1170 – testování průniku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b78b9b3-ee3c-48e0-a243-ed6dba5b7a12) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1170.json) |

### <a name="penetration-testing--independent-penetration-agent-or-team"></a>Testování průniku | Nezávislá agent průniku nebo tým

**ID**: NIST SP 800-53 R4 CA-8 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1171 – testování průniku – \| nezávislá agent průniku nebo tým](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d4820bc-8b61-4982-9501-2123cb776c00) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1171.json) |

### <a name="internal-system-connections"></a>Interní systémová připojení

**ID**: NIST SP 800-53 R4 CA-9 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1172 – interní systémová připojení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb43e946e-a4c8-4b92-8201-4a39331db43c) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1172.json) |
|[Microsoft Managed Control 1173 – interní systémová připojení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4aff9e7-2e60-46fa-86be-506b79033fc5) |Microsoft implementuje toto posouzení zabezpečení a řízení autorizací. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1173.json) |

## <a name="configuration-management"></a>Správa konfigurace

### <a name="configuration-management-policy-and-procedures"></a>Zásady a postupy správy konfigurace

**ID**: NIST SP 800-53 R4 cm-1- **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1174 – zásady a postupy správy konfigurace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F42a9a714-8fbb-43ac-b115-ea12d2bd652f) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1174.json) |
|[Microsoft Managed Control 1175 – zásady a postupy správy konfigurace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6dab4254-c30d-4bb7-ae99-1d21586c063c) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1175.json) |

### <a name="baseline-configuration"></a>Základní konfigurace

**ID**: NIST SP 800-53 R4 cm-2 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1176 – konfigurace standardních hodnot](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc30690a5-7bf3-467f-b0cd-ef5c7c7449cd) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1176.json) |

### <a name="baseline-configuration--reviews-and-updates"></a>Konfigurace standardních hodnot | Recenze a aktualizace

**ID**: NIST SP 800-53 R4 cm-2 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1177 – \| recenze a aktualizace konfigurace standardních hodnot](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F63dbc7a8-e20b-4d38-b857-a7f6c0cd94bc) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1177.json) |
|[Microsoft Managed Control 1178 – \| recenze a aktualizace konfigurace standardních hodnot](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7818b8f4-47c6-441a-90ae-12ce04e99893) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1178.json) |
|[Microsoft Managed Control 1179 – \| recenze a aktualizace konfigurace standardních hodnot](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3f9ce557-c8ab-4e6c-bb2c-9b8ed002c46c) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1179.json) |

### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>Konfigurace standardních hodnot | Podpora automatizace pro přesnost a měnu

**ID**: NIST SP 800-53 R4 cm-2 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1180 – podpora pro automatizaci konfigurace podle směrného plánu \| pro přesnost a měnu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F874e7880-a067-42a7-bcbe-1a340f54c8cc) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1180.json) |

### <a name="baseline-configuration--retention-of-previous-configurations"></a>Konfigurace standardních hodnot | Uchování předchozích konfigurací

**ID**: NIST SP 800-53 R4 cm-2 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1181 – \| uchování předchozích konfigurací podle směrného plánu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21839937-d241-4fa5-95c6-b669253d9ab9) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1181.json) |

### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Konfigurace standardních hodnot | Konfigurace systémů, součástí nebo zařízení pro vysoce rizikové oblasti

**ID**: NIST SP 800-53 R4 cm-2 (7) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1182 – konfigurace standardních hodnot \| Konfigurace systémů, součástí nebo zařízení pro vysoce rizikové oblasti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f34f554-da4b-4786-8d66-7915c90893da) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1182.json) |
|[Microsoft Managed Control 1183 – konfigurace standardních hodnot \| Konfigurace systémů, součástí nebo zařízení pro vysoce rizikové oblasti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5352e3e0-e63a-452e-9e5f-9c1d181cff9c) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1183.json) |

### <a name="configuration-change-control"></a>Ovládací prvek změny konfigurace

**ID**: NIST SP 800-53 R4 cm-3 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1184 – ovládací prvek změny konfigurace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13579d0e-0ab0-4b26-b0fb-d586f6d7ed20) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1184.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1185 – ovládací prvek změny konfigurace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6420cd73-b939-43b7-9d99-e8688fea053c) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1185.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1186 – ovládací prvek změny konfigurace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb95ba3bd-4ded-49ea-9d10-c6f4b680813d) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1186.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1187 – ovládací prvek změny konfigurace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9f2b2f9e-4ba6-46c3-907f-66db138b6f85) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1187.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1188 – ovládací prvek změny konfigurace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb20548a-c926-4e4d-855c-bcddc6faf95e) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1188.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1189 – ovládací prvek změny konfigurace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee45e02a-4140-416c-82c4-fecfea660b9d) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1189.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1190 – ovládací prvek změny konfigurace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc66a3d1e-465b-4f28-9da5-aef701b59892) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1190.json) |

### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Ovládací prvek změny konfigurace | Automatický dokument/oznámení/zákaz změn

**ID**: NIST SP 800-53 R4 cm-3 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1191 – ovládací prvek změny konfigurace – \| Automatický dokument/oznámení/zákaz změn](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f26a61b-a74d-467c-99cf-63644db144f7) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1191.json) |
|[Microsoft Managed Control 1192 – ovládací prvek změny konfigurace – \| Automatický dokument/oznámení/zákaz změn](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ebd97f7-b105-4f50-8daf-c51465991240) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1192.json) |
|[Microsoft Managed Control 1193 – ovládací prvek změny konfigurace – \| Automatický dokument/oznámení/zákaz změn](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff5fd629f-3075-4cae-ab53-bad65495a4ac) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1193.json) |
|[Microsoft Managed Control 1194 – ovládací prvek změny konfigurace – \| Automatický dokument/oznámení/zákaz změn](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc34667f-397e-4a65-9b72-d0358f0b6b09) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1194.json) |
|[Microsoft Managed Control 1195 – ovládací prvek změny konfigurace – \| Automatický dokument/oznámení/zákaz změn](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd1e1d65c-1013-4484-bd54-991332e6a0d2) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1195.json) |
|[Microsoft Managed Control 1196 – ovládací prvek změny konfigurace – \| Automatický dokument/oznámení/zákaz změn](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e7f4ea4-dd62-44f6-8886-ac6137cf52b0) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1196.json) |

### <a name="configuration-change-control--test--validate--document-changes"></a>Ovládací prvek změny konfigurace | Test/ověření/změny dokumentu

**ID**: NIST SP 800-53 R4 cm-3 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1197 – řízení změn konfigurace \| test/ověření/změny dokumentu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa20d2eaa-88e2-4907-96a2-8f3a05797e5c) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1197.json) |

### <a name="configuration-change-control--security-representative"></a>Ovládací prvek změny konfigurace | Bezpečnostní zástupce

**ID**: NIST SP 800-53 R4 cm-3 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1198 – zástupce zabezpečení správy změn konfigurace \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff56be5c3-660b-4c61-9078-f67cf072c356) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1198.json) |

### <a name="configuration-change-control--cryptography-management"></a>Ovládací prvek změny konfigurace | Správa kryptografie

**ID**: NIST SP 800-53 R4 cm-3 (6) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1199 – \| Správa kryptografie řízení změn konfigurace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9a08d1c-09b1-48f1-90ea-029bbdf7111e) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1199.json) |

### <a name="security-impact-analysis"></a>Analýza dopadu na zabezpečení

**ID**: NIST SP 800-53 R4 cm-4 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1200 – analýza dopadu na zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe98fe9d7-2ed3-44f8-93b7-24dca69783ff) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1200.json) |

### <a name="security-impact-analysis--separate-test-environments"></a>Analýza dopadu na zabezpečení | Samostatná testovací prostředí

**ID**: NIST SP 800-53 R4 cm-4 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1201 – analýza dopadu zabezpečení \| samostatná testovací prostředí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7daef997-fdd3-461b-8807-a608a6dd70f1) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1201.json) |

### <a name="access-restrictions-for-change"></a>Omezení přístupu pro změnu

**ID**: NIST SP 800-53 R4 cm-5 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1202 – omezení přístupu pro změnu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40a2a83b-74f2-4c02-ae65-f460a5d2792a) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1202.json) |

### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>Omezení přístupu pro změnu | Automatické vynucení přístupu/auditování

**ID**: NIST SP 800-53 R4 cm-5 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1203 – omezení přístupu pro změnu \| automatického vynucování přístupu nebo auditování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9012d14-e3e6-4d7b-b926-9f37b5537066) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1203.json) |

### <a name="access-restrictions-for-change--review-system-changes"></a>Omezení přístupu pro změnu | Revidovat změny systému

**ID**: NIST SP 800-53 R4 cm-5 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1204 – omezení přístupu pro \| změny systému změn Revize](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f4f6750-d1ab-4a4c-8dfd-af3237682665) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1204.json) |

### <a name="access-restrictions-for-change--signed-components"></a>Omezení přístupu pro změnu | Podepsané součásti

**ID**: NIST SP 800-53 R4 cm-5 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1205 – omezení přístupu pro změny \| podepsané součásti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b070cab-0fb8-4e48-ad29-fc90b4c2797c) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1205.json) |

### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Omezení přístupu pro změnu | Omezení produkčních a provozních oprávnění

**ID**: NIST SP 800-53 R4 cm-5 (5) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1206 – omezení přístupu pro změnu \| omezení produkční/provozní oprávnění](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0de232d-02a0-4652-872d-88afb4ae5e91) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1206.json) |
|[Microsoft Managed Control 1207 – omezení přístupu pro změnu \| omezení produkční/provozní oprávnění](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8713a0ed-0d1e-4d10-be82-83dffb39830e) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1207.json) |

### <a name="configuration-settings"></a>Nastavení konfigurace

**ID**: NIST SP 800-53 R4 cm-6 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravované řízení společnosti Microsoft 1208 – nastavení konfigurace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5ea87673-d06b-456f-a324-8abcee5c159f) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1208.json) |
|[Spravované řízení společnosti Microsoft 1209 – nastavení konfigurace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fce669c31-9103-4552-ae9c-cdef4e03580d) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1209.json) |
|[Spravované řízení společnosti Microsoft 1210 – nastavení konfigurace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3502c968-c490-4570-8167-1476f955e9b8) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1210.json) |
|[Spravované řízení společnosti Microsoft 1211 – nastavení konfigurace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a8b9dc8-6b00-4701-aa96-bba3277ebf50) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1211.json) |

### <a name="configuration-settings--automated-central-management--application--verification"></a>Nastavení konfigurace | Automatizovaná centrální Správa/aplikace/ověřování

**ID**: NIST SP 800-53 R4 cm-6 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1212 – nastavení konfigurace \| automatizovaná centrální Správa/aplikace/ověřování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56d970ee-4efc-49c8-8a4e-5916940d784c) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1212.json) |

### <a name="configuration-settings--respond-to-unauthorized-changes"></a>Nastavení konfigurace | Reakce na neoprávněné změny

**ID**: NIST SP 800-53 R4 cm-6 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravované řízení společnosti Microsoft 1213 – nastavení konfigurace \| reagují na neautorizované změny](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81f11e32-a293-4a58-82cd-134af52e2318) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1213.json) |

### <a name="least-functionality"></a>Nejnižší funkce

**ID**: NIST SP 800-53 R4 cm-7 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1214 – minimální funkce](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff714a4e2-b580-47b6-ae8c-f2812d3750f3) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1214.json) |
|[Microsoft Managed Control 1215 – minimální funkce](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88fc93e8-4745-4785-b5a5-b44bb92c44ff) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1215.json) |

### <a name="least-functionality--periodic-review"></a>Nejnižší funkce | Pravidelná kontrola

**ID**: NIST SP 800-53 R4 cm-7 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1216 – pravidelná kontrola s minimálními funkcemi \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7894fe6a-f5cb-44c8-ba90-c3f254ff9484) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1216.json) |
|[Microsoft Managed Control 1217 – pravidelná kontrola s minimálními funkcemi \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fedea4f20-b02c-4115-be75-86c080e5c0ed) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1217.json) |

### <a name="least-functionality--prevent-program-execution"></a>Nejnižší funkce | Zabránit spuštění programu

**ID**: NIST SP 800-53 R4 cm-7 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V počítačích by měly být povolené Adaptivní řízení aplikací pro definování bezpečných aplikací.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Povolit ovládací prvky aplikace, které definují seznam známých bezpečných aplikací spuštěných na vašich počítačích, a upozorní vás, když se spouštějí jiné aplikace. To pomáhá posílit zabezpečení vašich počítačů proti malwaru. Aby bylo možné zjednodušit proces konfigurace a údržby pravidel, Security Center využívá Machine Learning k analýze aplikací spuštěných na jednotlivých počítačích a k návrhu seznamu známých aplikací, které jsou v bezpečí. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Microsoft Managed Control 1218 – minimální funkce \| zabraňují spuštění programu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a1d0394-b9f5-493e-9e83-563fd0ac4df8) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1218.json) |

### <a name="least-functionality--authorized-software--whitelisting"></a>Nejnižší funkce | Autorizovaný software/seznam povolených

**ID**: NIST SP 800-53 R4 cm-7 (5) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V počítačích by měly být povolené Adaptivní řízení aplikací pro definování bezpečných aplikací.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Povolit ovládací prvky aplikace, které definují seznam známých bezpečných aplikací spuštěných na vašich počítačích, a upozorní vás, když se spouštějí jiné aplikace. To pomáhá posílit zabezpečení vašich počítačů proti malwaru. Aby bylo možné zjednodušit proces konfigurace a údržby pravidel, Security Center využívá Machine Learning k analýze aplikací spuštěných na jednotlivých počítačích a k návrhu seznamu známých aplikací, které jsou v bezpečí. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Microsoft Managed Control 1219 – minimální \| povolený software/seznam povolených funkcí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a39ac75-622b-4c88-9a3f-45b7373f7ef7) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1219.json) |
|[Microsoft Managed Control 1220 – minimální \| povolený software/seznam povolených funkcí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc40f31a7-81e1-4130-99e5-a02ceea2a1d6) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1220.json) |
|[Microsoft Managed Control 1221 – minimální \| povolený software/seznam povolených funkcí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22589a07-0007-486a-86ca-95355081ae2a) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1221.json) |

### <a name="information-system-component-inventory"></a>Inventář komponent informací systému

**ID**: NIST SP 800-53 R4 cm-8 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1222 – inventář komponent informací systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb39e62f-6bda-4558-8088-ec03d5670914) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1222.json) |
|[Microsoft Managed Control 1223 – inventář komponent informací systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05a1bb01-ad5a-49c1-aad3-b0c893b2ec3a) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1223.json) |

### <a name="information-system-component-inventory--updates-during-installations--removals"></a>Inventář komponent informací systému | Aktualizace během instalací/odebírání

**ID**: NIST SP 800-53 R4 cm-8 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1224 – aktualizace inventáře součásti informačního systému \| během instalací/odebrání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28cfa30b-7f72-47ce-ba3b-eed26c8d2c82) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1224.json) |

### <a name="information-system-component-inventory--automated-maintenance"></a>Inventář komponent informací systému | Automatizovaná údržba

**ID**: NIST SP 800-53 R4 cm-8 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1225 – automatizovaná údržba inventáře součásti systému informací \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8d096fe0-f510-4486-8b4d-d17dc230980b) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1225.json) |

### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Inventář komponent informací systému | Automatické neoprávněné zjišťování komponent

**ID**: NIST SP 800-53 R4 cm-8 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1226 – informační systém – inventarizace pro \| Automatické neoprávněné zjišťování komponent](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc158eb1c-ae7e-4081-8057-d527140c4e0c) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1226.json) |
|[Microsoft Managed Control 1227 – informační systém – inventarizace pro \| Automatické neoprávněné zjišťování komponent](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03b78f5e-4877-4303-b0f4-eb6583f25768) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1227.json) |

### <a name="information-system-component-inventory--accountability-information"></a>Inventář komponent informací systému | Informace o odpovědnosti

**ID**: NIST SP 800-53 R4 cm-8 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1228 – informace o odpovědnosti inventáře komponenty systému informací \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F39c54140-5902-4079-8bb5-ad31936fe764) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1228.json) |

### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>Inventář komponent informací systému | Žádné duplicitní monitorování součástí

**ID**: NIST SP 800-53 R4 cm-8 (5) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1229 – inventář komponent informačního systému \| bez duplicitního účtování součástí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03752212-103c-4ab8-a306-7e813022ca9d) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1229.json) |

### <a name="configuration-management-plan"></a>Plán správy konfigurace

**ID**: NIST SP 800-53 R4 cm-9 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1230 – plán správy konfigurace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F11158848-f679-4e9b-aa7b-9fb07d945071) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1230.json) |
|[Microsoft Managed Control 1231 – plán správy konfigurace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F244e0c05-cc45-4fe7-bf36-42dcf01f457d) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1231.json) |
|[Microsoft Managed Control 1232 – plán správy konfigurace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F396ba986-eac1-4d6d-85c4-d3fda6b78272) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1232.json) |
|[Microsoft Managed Control 1233 – plán správy konfigurace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d79001f-95fe-45d0-8736-f217e78c1f57) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1233.json) |

### <a name="software-usage-restrictions"></a>Omezení použití softwaru

**ID**: NIST SP 800-53 R4 cm-10 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1234 – omezení použití softwaru](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb293f881-361c-47ed-b997-bc4e2296bc0b) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1234.json) |
|[Microsoft Managed Control 1235 – omezení použití softwaru](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc49c610b-ece4-44b3-988c-2172b70d6e46) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1235.json) |
|[Microsoft Managed Control 1236 – omezení použití softwaru](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ba3ed84-c768-4e18-b87c-34ef1aff1b57) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1236.json) |

### <a name="software-usage-restrictions--open-source-software"></a>Omezení použití softwaru | Open source software

**ID**: NIST SP 800-53 R4 cm-10 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1237-omezení použití softwaru \| Open source software](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe80b6812-0bfa-4383-8223-cdd86a46a890) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1237.json) |

### <a name="user-installed-software"></a>Software nainstalovaný uživatelem

**ID**: NIST SP 800-53 R4 cm-11 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V počítačích by měly být povolené Adaptivní řízení aplikací pro definování bezpečných aplikací.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Povolit ovládací prvky aplikace, které definují seznam známých bezpečných aplikací spuštěných na vašich počítačích, a upozorní vás, když se spouštějí jiné aplikace. To pomáhá posílit zabezpečení vašich počítačů proti malwaru. Aby bylo možné zjednodušit proces konfigurace a údržby pravidel, Security Center využívá Machine Learning k analýze aplikací spuštěných na jednotlivých počítačích a k návrhu seznamu známých aplikací, které jsou v bezpečí. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Microsoft Managed Control 1238 – software nainstalovaný uživatelem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa36cedd4-3ffd-4b1f-8b18-aa71d8d87ce1) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1238.json) |
|[Microsoft Managed Control 1239 – software nainstalovaný uživatelem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0be51298-f643-4556-88af-d7db90794879) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1239.json) |
|[Microsoft Managed Control 1240 – software nainstalovaný uživatelem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F129eb39f-d79a-4503-84cd-92f036b5e429) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1240.json) |

### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>Software nainstalovaný uživatelem | Výstrahy pro neautorizované instalace

**ID**: NIST SP 800-53 R4 cm-11 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1241 – výstrahy softwaru nainstalované uživatelem \| pro neautorizované instalace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feca4d7b2-65e2-4e04-95d4-c68606b063c3) |Společnost Microsoft implementuje tento ovládací prvek správy konfigurace. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1241.json) |

## <a name="contingency-planning"></a>Plánování řešení nepředvídaných událostí

### <a name="contingency-planning-policy-and-procedures"></a>Zásady a postupy plánování pro nepředvídané řešení

**ID**: NIST SP 800-53 R4 CP-1 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1242 – zásady a postupy plánování nepředvídaných řešení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf3b3293-667a-445e-a722-fa0b0afc0958) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1242.json) |
|[Microsoft Managed Control 1243 – zásady a postupy plánování nepředvídaných řešení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca9a4469-d6df-4ab2-a42f-1213c396f0ec) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1243.json) |

### <a name="contingency-plan"></a>Pohotovostní plán

**ID**: NIST SP 800-53 R4 CP-2 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1244 – plán řešení nepředvídaných událostí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a13a8f8-c163-4b1b-8554-d63569dab937) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1244.json) |
|[Microsoft Managed Control 1245 – plán řešení nepředvídaných událostí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0e45314-57b8-4623-80cd-bbb561f59516) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1245.json) |
|[Microsoft Managed Control 1246 – plán řešení nepředvídaných událostí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F398eb61e-8111-40d5-a0c9-003df28f1753) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1246.json) |
|[Microsoft Managed Control 1247 – plán řešení nepředvídaných událostí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e666db5-b2ef-4b06-aac6-09bfce49151b) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1247.json) |
|[Microsoft Managed Control 1248 – plán řešení nepředvídaných událostí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50fc602d-d8e0-444b-a039-ad138ee5deb0) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1248.json) |
|[Microsoft Managed Control 1249 – plán řešení nepředvídaných událostí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd3bf4251-0818-42db-950b-afd5b25a51c2) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1249.json) |
|[Microsoft Managed Control 1250 – plán řešení nepředvídaných událostí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8de614d8-a8b7-4f70-a62a-6d37089a002c) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1250.json) |

### <a name="contingency-plan--coordinate-with-related-plans"></a>Pohotovostní plán | Koordinace se souvisejícími plány

**ID**: NIST SP 800-53 R4 CP-2 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1251 – souřadnice plánu \| pro pohotovostní plán se souvisejícími plány](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5e2b3730-8c14-4081-8893-19dbb5de7348) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1251.json) |

### <a name="contingency-plan--capacity-planning"></a>Pohotovostní plán | Plánování kapacity

**ID**: NIST SP 800-53 R4 CP-2 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1252 – plánování kapacity pro pohotovostní plán \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa328fd72-8ff5-4f96-8c9c-b30ed95db4ab) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1252.json) |

### <a name="contingency-plan--resume-essential-missions--business-functions"></a>Pohotovostní plán | Obnovení základních misí/obchodních funkcí

**ID**: NIST SP 800-53 R4 CP-2 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1253 – plán pro pohotovostní \| činnost obnovení základních misí/obchodních funkcí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0afce0b3-dd9f-42bb-af28-1e4284ba8311) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1253.json) |

### <a name="contingency-plan--resume-all-missions--business-functions"></a>Pohotovostní plán | Obnovit všechny pracovní úkoly/obchodní funkce

**ID**: NIST SP 800-53 R4 CP-2 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1254 – plán pro pohotovostní \| činnost obnoví všechny pracovní funkce a obchodní funkce.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F704e136a-4fe0-427c-b829-cd69957f5d2b) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1254.json) |

### <a name="contingency-plan--continue--essential-missions--business-functions"></a>Pohotovostní plán | Pokračovat v základních misích a obchodních funkcích

**ID**: NIST SP 800-53 R4 CP-2 (5) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1255 – plán pro pohotovostní \| činnost pokračuje v základních misích a obchodních funkcích.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff3793f5e-937f-44f7-bfba-40647ef3efa0) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1255.json) |

### <a name="contingency-plan--identify-critical-assets"></a>Pohotovostní plán | Identifikace důležitých prostředků

**ID**: NIST SP 800-53 R4 CP-2 (8) **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1256 – plán pro pohotovostní \| identifikaci kritických prostředků](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F232ab24b-810b-4640-9019-74a7d0d6a980) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1256.json) |

### <a name="contingency-training"></a>Pohotovostní školení

**ID**: NIST SP 800-53 R4 CP-3 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1257-pohotovostní školení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb958b241-4245-4bd6-bd2d-b8f0779fb543) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1257.json) |
|[Microsoft Managed Control 1258-pohotovostní školení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7814506c-382c-4d33-a142-249dd4a0dbff) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1258.json) |
|[Microsoft Managed Control 1259-pohotovostní školení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d9e18f7-bad9-4d30-8806-a0c9d5e26208) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1259.json) |

### <a name="contingency-training--simulated-events"></a>Pohotovostní školení | Simulované události

**ID**: NIST SP 800-53 R4 CP-3 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1260 – simulace neočekávaných kurzů – \| simulované události](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F42254fc4-2738-4128-9613-72aaa4f0d9c3) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1260.json) |

### <a name="contingency-plan-testing"></a>Testování plánu pro nepředvídané účely

**ID**: NIST SP 800-53 R4 CP-4 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1261 – testování plánu pro nepředvídané účely](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F65aeceb5-a59c-4cb1-8d82-9c474be5d431) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1261.json) |
|[Microsoft Managed Control 1262 – testování plánu pro nepředvídané účely](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F831e510e-db41-4c72-888e-a0621ab62265) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1262.json) |
|[Microsoft Managed Control 1263 – testování plánu pro nepředvídané účely](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41472613-3b05-49f6-8fe8-525af113ce17) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1263.json) |

### <a name="contingency-plan-testing--coordinate-with-related-plans"></a>Testování plánu pro nepředvídané účely | Koordinace se souvisejícími plány

**ID**: NIST SP 800-53 R4 CP-4 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1264 – koordinace plánů řešení nepředvídaných testů \| se souvisejícími plány](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd280d4b-50a1-42fb-a479-ece5878acf19) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1264.json) |

### <a name="contingency-plan-testing--alternate-processing-site"></a>Testování plánu pro nepředvídané účely | Lokalita alternativního zpracování

**ID**: NIST SP 800-53 R4 CP-4 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1265 – \| lokalita alternativního zpracování testování plánu pro nepředvídané účely](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa18adb5b-1db6-4a5b-901a-7d3797d12972) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1265.json) |
|[Microsoft Managed Control 1266 – \| lokalita alternativního zpracování testování plánu pro nepředvídané účely](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b4a3eb2-c25d-40bf-ad41-5094b6f59cee) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1266.json) |

### <a name="alternate-storage-site"></a>Alternativní lokalita úložiště

**ID**: NIST SP 800-53 R4 CP-6 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1267 – alternativní lokalita úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e97ba1d-be5d-4953-8da4-0cccf28f4805) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1267.json) |
|[Microsoft Managed Control 1268 – alternativní lokalita úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F23f6e984-3053-4dfc-ab48-543b764781f5) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1268.json) |

### <a name="alternate-storage-site--separation-from-primary-site"></a>Lokalita alternativních úložišť | Oddělení z primární lokality

**ID**: NIST SP 800-53 R4 CP-6 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1269 – alternativní oddělení lokality úložiště \| z primární lokality](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F19b9439d-865d-4474-b17d-97d2702fdb66) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1269.json) |

### <a name="alternate-storage-site--recovery-time--point-objectives"></a>Lokalita alternativních úložišť | Cíle pro čas a bod obnovení

**ID**: NIST SP 800-53 R4 CP-6 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1270 – alternativní cíle služby Storage site \| Recovery/bodu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53c76a39-2097-408a-b237-b279f7b4614d) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1270.json) |

### <a name="alternate-storage-site--accessibility"></a>Lokalita alternativních úložišť | Přístup

**ID**: NIST SP 800-53 R4 CP-6 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1271 – alternativní přístupnost lokality úložiště \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda3bfb53-9c46-4010-b3db-a7ba1296dada) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1271.json) |

### <a name="alternate-processing-site"></a>Lokalita alternativního zpracování

**ID**: NIST SP 800-53 R4 CP-7 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Auditovat virtuální počítače bez nakonfigurovaného zotavení po havárii](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Auditujte virtuální počítače, u kterých není nakonfigurované zotavení po havárii. Další informace o zotavení po havárii najdete v [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Microsoft Managed Control 1272 – alternativní server pro zpracování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae46cf7a-e3fd-427b-9b91-44bc78e2d9d8) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1272.json) |
|[Microsoft Managed Control 1273 – alternativní server pro zpracování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe77fcbf2-a1e8-44f1-860e-ed6583761e65) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1273.json) |
|[Microsoft Managed Control 1274 – alternativní server pro zpracování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2aee175f-cd16-4825-939a-a85349d96210) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1274.json) |

### <a name="alternate-processing-site--separation-from-primary-site"></a>Lokalita alternativního zpracování | Oddělení z primární lokality

**ID**: NIST SP 800-53 R4 CP-7 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1275 – alternativní zpracování \| oddělení lokality z primární lokality](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa23d9d53-ad2e-45ef-afd5-e6d10900a737) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1275.json) |

### <a name="alternate-processing-site--accessibility"></a>Lokalita alternativního zpracování | Přístup

**ID**: NIST SP 800-53 R4 CP-7 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1276 – alternativní zpracování \| přístupnost webu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe214e563-1206-4a43-a56b-ac5880c9c571) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1276.json) |

### <a name="alternate-processing-site--priority-of-service"></a>Lokalita alternativního zpracování | Priorita služby

**ID**: NIST SP 800-53 R4 CP-7 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1277 – alternativní pracovní \| Priorita pro lokalitu služby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdc43e829-3d50-4a0a-aa0f-428d551862aa) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1277.json) |

### <a name="alternate-processing-site--preparation-for-use"></a>Lokalita alternativního zpracování | Příprava pro použití

**ID**: NIST SP 800-53 R4 CP-7 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1278 – alternativní zpracování \| Příprava lokality pro použití](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8e5ef485-9e16-4c53-a475-fbb8107eac59) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1278.json) |

### <a name="telecommunications-services"></a>Telekomunikační služby

**ID**: NIST SP 800-53 R4 CP-8 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1279 – telekomunikační služby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d00bcd6-963d-4c02-ad8e-b45fa50bf3b0) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1279.json) |

### <a name="telecommunications-services--priority-of-service-provisions"></a>Telekomunikační služby | Priorita ustanovení služby

**ID**: NIST SP 800-53 R4 CP-8 (1) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1280 – \| pravidla služeb pro telekomunikační služby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa108498-b3a8-4ffb-9e79-1107e76afad3) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1280.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1281 – \| pravidla služeb pro telekomunikační služby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dc459b3-0e77-45af-8d71-cfd8c9654fe2) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1281.json) |

### <a name="telecommunications-services--single-points-of-failure"></a>Telekomunikační služby | Selhání v jednom bodě

**ID**: NIST SP 800-53 R4 CP-8 (2) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1282 – \| jeden bod selhání pro telekomunikační služby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34042a97-ec6d-4263-93d2-8c1c46823b2a) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1282.json) |

### <a name="telecommunications-services--separation-of-primary--alternate-providers"></a>Telekomunikační služby | Oddělení primárních nebo alternativních zprostředkovatelů

**ID**: NIST SP 800-53 R4 CP-8 (3) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1283 – oddělení služeb \| primárního a alternativního poskytovatele služby telekomunikace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9172e76-7f56-46e9-93bf-75d69bdb5491) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1283.json) |

### <a name="telecommunications-services--provider-contingency-plan"></a>Telekomunikační služby | Pohotovostní plán poskytovatele

**ID**: NIST SP 800-53 R4 CP-8 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1284 – \| pohotovostní plán poskytovatele telekomunikačních služeb](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F942b3e97-6ae3-410e-a794-c9c999b97c0b) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1284.json) |
|[Microsoft Managed Control 1285 – \| pohotovostní plán poskytovatele telekomunikačních služeb](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01f7726b-db54-45c2-bcb5-9bd7a43796ee) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1285.json) |
|[Microsoft Managed Control 1286 – \| pohotovostní plán poskytovatele telekomunikačních služeb](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4f9b47a-2116-4e6f-88db-4edbf22753f1) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1286.json) |

### <a name="information-system-backup"></a>Zálohování informací systému

**ID**: NIST SP 800-53 R4 CP-9 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1287 – informace o zálohování systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F819dc6da-289d-476e-8500-7e341ef8677d) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1287.json) |
|[Microsoft Managed Control 1288 – informace o zálohování systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8d854c3b-a3e6-4ec9-9f0c-c7274dbaeb2f) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1288.json) |
|[Microsoft Managed Control 1289 – informace o zálohování systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a724864-956a-496c-b778-637cb1d762cf) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1289.json) |
|[Microsoft Managed Control 1290 – informace o zálohování systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F92f85ce9-17b7-49ea-85ee-ea7271ea6b82) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1290.json) |

### <a name="information-system-backup--testing-for-reliability--integrity"></a>Zálohování informací systému | Testování spolehlivosti/integrity

**ID**: NIST SP 800-53 R4 CP-9 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1291 – informace o \| testování spolehlivosti a integrity v systémovém zálohování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d8fd073-9c85-4ee2-a9d0-2e4ec9eb8912) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1291.json) |

### <a name="information-system-backup--test-restoration-using-sampling"></a>Zálohování informací systému | Testování obnovení pomocí vzorkování

**ID**: NIST SP 800-53 R4 CP-9 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1292 – obnovení záložního \| testovacího systému pomocí vzorkování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd03516cf-0293-489f-9b32-a18f2a79f836) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1292.json) |

### <a name="information-system-backup--separate-storage-for-critical-information"></a>Zálohování informací systému | Samostatné úložiště pro důležité informace

**ID**: NIST SP 800-53 R4 CP-9 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1293 – informace o zálohování systému \| oddělené úložiště pro důležité informace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87f7cd82-2e45-4d0f-9e2f-586b0962d142) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1293.json) |

### <a name="information-system-backup--transfer-to-alternate-storage-site"></a>Zálohování informací systému | Přenos na alternativní lokalitu úložiště

**ID**: NIST SP 800-53 R4 CP-9 (5) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1294-Information System Backup \| Transfer do alternativní lokality úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F49dbe627-2c1e-438c-979e-dd7a39bbf81d) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1294.json) |

### <a name="information-system-recovery-and-reconstitution"></a>Obnovení a rebudování informací o systému

**ID**: NIST SP 800-53 R4 CP-10 **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1295 – informace o obnovení a rebudování systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa895fbdb-204d-4302-9689-0a59dc42b3d9) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1295.json) |

### <a name="information-system-recovery-and-reconstitution--transaction-recovery"></a>Obnovení a obnova informací systému | Obnovení transakce

**ID**: NIST SP 800-53 R4 CP-10 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1296 – obnovení transakcí systému informací o obnovení a reústavy \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe57b98a0-a011-4956-a79d-5d17ed8b8e48) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1296.json) |

### <a name="information-system-recovery-and-reconstitution--restore-within-time-period"></a>Obnovení a obnova informací systému | Obnovení v časovém období

**ID**: NIST SP 800-53 R4 CP-10 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1297 – informační systém obnovení a \| obnovení v časovém období](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93fd8af1-c161-4bae-9ba9-f62731f76439) |Microsoft implementuje tuto pohotovostní kontrolu plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1297.json) |

## <a name="identification-and-authentication"></a>Identifikace a ověřování

### <a name="identification-and-authentication-policy-and-procedures"></a>Zásady a postupy pro identifikaci a ověřování

**ID**: NIST SP 800-53 R4 IA-1 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1298 – zásady a postupy pro identifikaci a ověřování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1dc784b5-4895-4d27-9d40-a06b032bd1ee) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1298.json) |
|[Microsoft Managed Control 1299 – zásady a postupy pro identifikaci a ověřování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd4e54f7-9ab0-4bae-b6cc-457809948a89) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1299.json) |

### <a name="identification-and-authentication-organizational-users"></a>Identifikace a ověřování (uživatelé organizace)

**ID**: NIST SP 800-53 R4 IA-2 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1300 – identifikace a ověřování (uživatelé organizace)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F99deec7d-5526-472e-b07c-3645a792026a) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1300.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Identifikace a ověřování (uživatelé organizace) | Síťový přístup k privilegovaným účtům

**ID**: NIST SP 800-53 R4 IA-2 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V rámci vašeho předplatného by měly být povolené účty s oprávněním k zápisu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) by mělo být povolené pro všechny účty předplatného s oprávněním k zápisu, aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) by mělo být povolené pro všechny účty předplatného s oprávněním vlastníka, aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[Microsoft Managed Control 1301 – identifikace a ověřování (org. Users) \| síťový přístup k privilegovaným účtům](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6a8e0cc-ac23-468b-abe4-a8a1cc6d7a08) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1301.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Identifikace a ověřování (uživatelé organizace) | Přístup k síti bez privilegovaných účtů

**ID**: NIST SP 800-53 R4 IA-2 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |U všech účtů předplatného s oprávněním ke čtení by se mělo povolit Multi-Factor Authentication (MFA), aby se zabránilo narušení účtů nebo prostředků. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |
|[Microsoft Managed Control 1302 – identifikace a ověřování (org. Users) \| přístup k síti bez privilegovaných účtů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09828c65-e323-422b-9774-9d5c646124da) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1302.json) |

### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>Identifikace a ověřování (uživatelé organizace) | Místní přístup k privilegovaným účtům

**ID**: NIST SP 800-53 R4 IA-2 (3) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1303 – identifikace a ověřování (org. Users) \| místní přístup k privilegovaným účtům](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F80ca0a27-918a-4604-af9e-723a27ee51e8) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1303.json) |

### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>Identifikace a ověřování (uživatelé organizace) | Místní přístup k neprivilegovaným účtům

**ID**: NIST SP 800-53 R4 IA-2 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1304 – identifikace a ověřování (org. Users) \| místní přístup k neprivilegovaným účtům](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6ca71be3-16cb-4d39-8b50-7f8fd5e2f11b) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1304.json) |

### <a name="identification-and-authentication-organizational-users--group-authentication"></a>Identifikace a ověřování (uživatelé organizace) | Ověřování skupiny

**ID**: NIST SP 800-53 R4 IA-2 (5) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Ověřování skupiny Microsoft Managed Control 1305-Identification and Authentication (org. Users) \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d9166a8-1722-4b8f-847c-2cf3f2618b3d) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1305.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>Identifikace a ověřování (uživatelé organizace) | Síťový přístup k privilegovaným účtům – odolné přehrávání

**ID**: NIST SP 800-53 R4 IA-2 (8) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1306-Identification and Authentication (org. Users) \| net. Přístup k priv. Accts.-Play](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcafc6c3c-5fc5-4c5e-a99b-a0ccb1d34eff) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1306.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>Identifikace a ověřování (uživatelé organizace) | Přístup k síti bez privilegovaných účtů – přehrajte do odolného

**ID**: NIST SP 800-53 R4 IA-2 (9) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1307-Identification and Authentication (org. Users) \| net. Přístup k jiným než priv. Accts.-Play](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84e622c8-4bed-417c-84c6-b2fb0dd73682) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1307.json) |

### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>Identifikace a ověřování (uživatelé organizace) | Vzdálený přístup – samostatné zařízení

**ID**: NIST SP 800-53 R4 IA-2 (11) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1308-Identification and Authentication (org. Users) \| Remote Access – samostatné zařízení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81817e1c-5347-48dd-965a-40159d008229) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1308.json) |

### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>Identifikace a ověřování (uživatelé organizace) | Přijetí přihlašovacích údajů piv

**ID**: NIST SP 800-53 R4 IA-2 (12) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1309 – identifikace a ověřování (org. Users) \| přijetí přihlašovacích údajů piv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff355d62b-39a8-4ba3-abf7-90f71cb3b000) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1309.json) |

### <a name="device-identification-and-authentication"></a>Identifikace a ověřování zařízení

**ID**: NIST SP 800-53 R4 IA-3 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1310 – identifikace zařízení a jeho ověřování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F450d7ede-823d-4931-a99d-57f6a38807dc) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1310.json) |

### <a name="identifier-management"></a>Správa identifikátorů

**ID**: NIST SP 800-53 R4 IA-4 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1311 – Správa identifikátorů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe7568697-0c9e-4ea3-9cec-9e567d14f3c6) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1311.json) |
|[Spravovaný ovládací prvek Microsoft 1312 – Správa identifikátorů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d6a5968-9eef-4c18-8534-376790ab7274) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1312.json) |
|[Spravovaný ovládací prvek Microsoft 1313 – Správa identifikátorů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36220f5b-79a1-4cdb-8c74-2d2449f9a510) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1313.json) |
|[Spravovaný ovládací prvek Microsoft 1314 – Správa identifikátorů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef0c8530-efd9-45b8-b753-f03083d06295) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1314.json) |
|[Spravovaný ovládací prvek Microsoft 1315 – Správa identifikátorů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87116-f1a1-4edb-bfbf-14e036f8d454) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1315.json) |

### <a name="identifier-management--identify-user-status"></a>Správa identifikátorů | Identifikovat stav uživatele

**ID**: NIST SP 800-53 R4 IA-4 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1316 – Správa identifikátorů \| identifikuje stav uživatele](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8ce14753-66e5-465d-9841-26ef55c09c0d) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1316.json) |

### <a name="authenticator-management"></a>Správa ověřovatelů

**ID**: NIST SP 800-53 R4 IA-5 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Přidat spravovanou identitu přiřazenou systémem a povolit přiřazení konfigurace hostů na virtuálních počítačích bez identit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |Tato zásada přidává spravovanou identitu přiřazenou systémem do virtuálních počítačů hostovaných v Azure, které jsou podporované konfigurací hosta, ale nemají žádné spravované identity. Spravovaná identita přiřazená systémem je předpokladem pro všechna přiřazení konfigurace hostů a musí se do počítačů přidat před použitím libovolných definic zásad konfigurace hostů. Další informace o konfiguraci hosta najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |upravíte |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[Přidat spravovanou identitu přiřazenou systémem a povolit přiřazení konfigurace hostů na virtuálních počítačích s identitou přiřazenou uživatelem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |Tato zásada přidává spravovanou identitu přiřazenou systémem do virtuálních počítačů hostovaných v Azure, které podporuje konfigurace hostů, a má alespoň jednu identitu přiřazenou uživatelem, ale nemá spravovanou identitu přiřazenou systémem. Spravovaná identita přiřazená systémem je předpokladem pro všechna přiřazení konfigurace hostů a musí se do počítačů přidat před použitím libovolných definic zásad konfigurace hostů. Další informace o konfiguraci hosta najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |upravíte |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[Auditovat počítače se systémem Linux, které nemají oprávnění k souboru passwd nastavené na 0644](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6955644-301c-44b5-a4c4-528577de6861) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud počítače se systémem Linux, které nemají oprávnění k souboru passwd, nejsou nastavené na 0644. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword121_AINE.json) |
|[Auditovat počítače se systémem Linux, které mají účty bez hesla](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6ec09a3-78bf-4f8f-99dc-6c77182d0f99) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud počítače se systémem Linux, které mají účty bez hesla. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword232_AINE.json) |
|[Auditovat počítače s Windows, které neukládají hesla pomocí reverzibilního šifrování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud počítače s Windows, které neukládají hesla pomocí reverzibilního šifrování. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[Nasazení rozšíření konfigurace hosta pro Linux k povolení přiřazení konfigurace hostů na virtuálních počítačích se systémem Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |Tato zásada nasadí rozšíření konfigurace hosta pro Linux na virtuální počítače Linux hostované v Azure, které jsou podporované konfigurací hosta. Rozšíření konfigurace hosta pro Linux je předpokladem pro všechna přiřazení konfigurace hosta systému Linux a musí být nasazena do počítačů před použitím jakékoli definice zásad konfigurace hosta systému Linux. Další informace o konfiguraci hosta najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Nasazení rozšíření konfigurace hosta systému Windows pro povolení přiřazení konfigurace hostů na virtuálních počítačích s Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |Tato zásada nasadí rozšíření konfigurace hosta systému Windows na virtuální počítače s Windows hostované v Azure, které jsou podporované konfigurací hosta. Rozšíření konfigurace hosta systému Windows je předpokladem pro všechna přiřazení konfigurace hostů systému Windows a musí být nasazena do počítačů před použitím jakékoli definice zásad konfigurace služby Windows Host. Další informace o konfiguraci hosta najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Spravovaný ovládací prvek Microsoft 1317 – Správa ověřovacích dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8877f519-c166-47b7-81b7-8a8eb4ff3775) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1317.json) |
|[Spravovaný ovládací prvek Microsoft 1318 – Správa ověřovacích dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffced5fda-3bdb-4d73-bfea-0e2c80428b66) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1318.json) |
|[Spravovaný ovládací prvek Microsoft 1319 – Správa ověřovacích dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F66f7ae57-5560-4fc5-85c9-659f204e7a42) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1319.json) |
|[Spravovaný ovládací prvek Microsoft 1320 – Správa ověřovacích dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6f54c732-71d4-4f93-a696-4e373eca3a77) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1320.json) |
|[Spravovaný ovládací prvek Microsoft 1321 – Správa ověřovacích dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb627cc6-3a9d-46b5-96b7-5fca49178a37) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1321.json) |
|[Spravovaný ovládací prvek Microsoft 1322 – Správa ověřovacích dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d1d971e-467e-4278-9633-c74c3d4fecc4) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1322.json) |
|[Spravovaný ovládací prvek Microsoft 1323 – Správa ověřovacích dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabe8f70b-680f-470c-9b86-a7edfb664ecc) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1323.json) |
|[Spravovaný ovládací prvek Microsoft 1324 – Správa ověřovacích dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cfea2b3-7f77-497e-ac20-0752f2ff6eee) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1324.json) |
|[Spravovaný ovládací prvek Microsoft 1325 – Správa ověřovacích dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1845796a-7581-49b2-ae20-443121538e19) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1325.json) |
|[Spravovaný ovládací prvek Microsoft 1326 – Správa ověřovacích dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8605fc00-1bf5-4fb3-984e-c95cec4f231d) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1326.json) |

### <a name="authenticator-management--password-based-authentication"></a>Správa ověřovatele | Ověřování založené na heslech

**ID**: NIST SP 800-53 R4 IA-5 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Přidat spravovanou identitu přiřazenou systémem a povolit přiřazení konfigurace hostů na virtuálních počítačích bez identit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |Tato zásada přidává spravovanou identitu přiřazenou systémem do virtuálních počítačů hostovaných v Azure, které jsou podporované konfigurací hosta, ale nemají žádné spravované identity. Spravovaná identita přiřazená systémem je předpokladem pro všechna přiřazení konfigurace hostů a musí se do počítačů přidat před použitím libovolných definic zásad konfigurace hostů. Další informace o konfiguraci hosta najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |upravíte |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[Přidat spravovanou identitu přiřazenou systémem a povolit přiřazení konfigurace hostů na virtuálních počítačích s identitou přiřazenou uživatelem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |Tato zásada přidává spravovanou identitu přiřazenou systémem do virtuálních počítačů hostovaných v Azure, které podporuje konfigurace hostů, a má alespoň jednu identitu přiřazenou uživatelem, ale nemá spravovanou identitu přiřazenou systémem. Spravovaná identita přiřazená systémem je předpokladem pro všechna přiřazení konfigurace hostů a musí se do počítačů přidat před použitím libovolných definic zásad konfigurace hostů. Další informace o konfiguraci hosta najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |upravíte |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[Auditovat počítače s Windows, které umožňují opakované použití předchozích 24 hesel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b054a0d-39e2-4d53-bea3-9734cad2c69b) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud počítače s Windows, které umožňují opakované použití předchozích 24 hesel. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEnforce_AINE.json) |
|[Audit počítačů s Windows, které nemají maximální stáří hesla 70 dnů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ceb8dc2-559c-478b-a15b-733fbf1e3738) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nevyhovující, pokud počítače s Windows, které nemají maximální stáří hesla 70 dnů, nejsou kompatibilní. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsMaximumPassword_AINE.json) |
|[Audit počítačů s Windows, které nemají minimální stáří hesla 1 den](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F237b38db-ca4d-4259-9e47-7882441ca2c0) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud počítače s Windows, které nemají minimální stáří hesla 1 den. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsMinimumPassword_AINE.json) |
|[Auditovat počítače s Windows, které nemají povolené nastavení složitost hesla](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf16e0bb-31e1-4646-8202-60a235cc7e74) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud jsou počítače s Windows, u kterých není povolené nastavení složitosti hesla. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordComplexity_AINE.json) |
|[Auditovat počítače s Windows, které neomezují minimální délku hesla na 14 znaků](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2d0e922-65d0-40c4-8f87-ea6da2d307a2) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud počítače s Windows, které neomezují minimální délku hesla na 14 znaků. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordLength_AINE.json) |
|[Auditovat počítače s Windows, které neukládají hesla pomocí reverzibilního šifrování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud počítače s Windows, které neukládají hesla pomocí reverzibilního šifrování. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[Nasazení rozšíření konfigurace hosta pro Linux k povolení přiřazení konfigurace hostů na virtuálních počítačích se systémem Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |Tato zásada nasadí rozšíření konfigurace hosta pro Linux na virtuální počítače Linux hostované v Azure, které jsou podporované konfigurací hosta. Rozšíření konfigurace hosta pro Linux je předpokladem pro všechna přiřazení konfigurace hosta systému Linux a musí být nasazena do počítačů před použitím jakékoli definice zásad konfigurace hosta systému Linux. Další informace o konfiguraci hosta najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Nasazení rozšíření konfigurace hosta systému Windows pro povolení přiřazení konfigurace hostů na virtuálních počítačích s Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |Tato zásada nasadí rozšíření konfigurace hosta systému Windows na virtuální počítače s Windows hostované v Azure, které jsou podporované konfigurací hosta. Rozšíření konfigurace hosta systému Windows je předpokladem pro všechna přiřazení konfigurace hostů systému Windows a musí být nasazena do počítačů před použitím jakékoli definice zásad konfigurace služby Windows Host. Další informace o konfiguraci hosta najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Microsoft Managed Control 1327 – \| ověřování pomocí hesla správy ověřovatele](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03188d8f-1ae5-4fe1-974d-2d7d32ef937d) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1327.json) |
|[Microsoft Managed Control 1328 – \| ověřování pomocí hesla správy ověřovatele](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff5c66fdc-3d02-4034-9db5-ba57802609de) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1328.json) |
|[Microsoft Managed Control 1329 – \| ověřování pomocí hesla správy ověřovatele](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F498f6234-3e20-4b6a-a880-cbd646d973bd) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1329.json) |
|[Microsoft Managed Control 1330 – \| ověřování pomocí hesla správy ověřovatele](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff75cedb2-5def-4b31-973e-b69e8c7bd031) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1330.json) |
|[Microsoft Managed Control 1331 – \| ověřování pomocí hesla správy ověřovatele](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05460fe2-301f-4ed1-8174-d62c8bb92ff4) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1331.json) |
|[Microsoft Managed Control 1332 – \| ověřování pomocí hesla správy ověřovatele](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F068260be-a5e6-4b0a-a430-cd27071c226a) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1332.json) |

### <a name="authenticator-management--pki-based-authentication"></a>Správa ověřovatele | Ověřování založené na infrastruktuře PKI

**ID**: NIST SP 800-53 R4 IA-5 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1333 – \| ověřování založené na infrastruktuře PKI pro správu ověřovatele](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3298d6bf-4bc6-4278-a95d-f7ef3ac6e594) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1333.json) |
|[Microsoft Managed Control 1334 – \| ověřování založené na infrastruktuře PKI pro správu ověřovatele](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44bfdadc-8c2e-4c30-9c99-f005986fabcd) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1334.json) |
|[Microsoft Managed Control 1335 – \| ověřování založené na infrastruktuře PKI pro správu ověřovatele](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F382016f3-d4ba-4e15-9716-55077ec4dc2a) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1335.json) |
|[Microsoft Managed Control 1336 – \| ověřování založené na infrastruktuře PKI pro správu ověřovatele](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F77f56280-e367-432a-a3b9-8ca2aa636a26) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1336.json) |

### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>Správa ověřovatele | Registrace v případě osobně nebo důvěryhodné třetí strany

**ID**: NIST SP 800-53 R4 IA-5 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1337 – Správa ověřovacích dat \| v osobní nebo důvěryhodné registraci třetí strany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F463e5220-3f79-4e24-a63f-343e4096cd22) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1337.json) |

### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>Správa ověřovatele | Automatizovaná podpora pro určení síly hesla

**ID**: NIST SP 800-53 R4 IA-5 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1338 – \| automatizovaná Podpora správy ověřovatelů pro určení síly hesla](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6c59a207-6aed-41dc-83a2-e1ff66e4a4db) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1338.json) |

### <a name="authenticator-management--protection-of-authenticators"></a>Správa ověřovatele | Ochrana ověřovatelů

**ID**: NIST SP 800-53 R4 IA-5 (6) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1339 – ochrana ověřovatelů pro správu ověřovacích dat \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F367ae386-db7f-4167-b672-984ff86277c0) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1339.json) |

### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>Správa ověřovatele | Žádné vložené nešifrované statické ověřovací objekty

**ID**: NIST SP 800-53 R4 IA-5 (7) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1340 – Správa ověřovacích dat \| nemá žádné vložené nešifrované statické ověřovací objekty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe51ff84b-e5ea-408f-b651-2ecc2933e4c6) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1340.json) |

### <a name="authenticator-management--multiple-information-system-accounts"></a>Správa ověřovatele | Více účtů systému informací

**ID**: NIST SP 800-53 R4 IA-5 (8) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1341 – Správa ověřovacích dat \| více účtů systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34cb7e92-fe4c-4826-b51e-8cd203fa5d35) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1341.json) |

### <a name="authenticator-management--hardware-token-based-authentication"></a>Správa ověřovatele | Ověřování na základě hardwarových tokenů

**ID**: NIST SP 800-53 R4 IA-5 (11) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1342- \| ověřování na základě hardwarových tokenů správy ověřovatele](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F283a4e29-69d5-4c94-b99e-29acf003c899) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1342.json) |

### <a name="authenticator-management--expiration-of-cached-authenticators"></a>Správa ověřovatele | Vypršení platnosti ověřovatelů uložených v mezipaměti

**ID**: NIST SP 800-53 R4 IA-5 (13) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1343 – \| vypršení platnosti ověřovacích dat uložených v mezipaměti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c251a55-31eb-4e53-99c6-e9c43c393ac2) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1343.json) |

### <a name="authenticator-feedback"></a>Zpětná vazba ověřovatele

**ID**: NIST SP 800-53 R4 IA-6 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1344 – zpětná vazba ověřovatele](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c895fe7-2d8e-43a2-838c-3a533a5b355e) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1344.json) |

### <a name="cryptographic-module-authentication"></a>Ověřování kryptografických modulů

**ID**: NIST SP 800-53 R4 IA-7 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1345 – ověřování kryptografických modulů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff86aa129-7c07-4aa4-bbf5-792d93ffd9ea) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1345.json) |

### <a name="identification-and-authentication-non-organizational-users"></a>Identifikace a ověřování (uživatelé mimo organizaci)

**ID**: NIST SP 800-53 R4 IA-8 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1346 – identifikace a ověřování (uživatelé bez organizace)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F464dc8ce-2200-4720-87a5-dc5952924cc6) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1346.json) |

### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>Identifikace a ověřování (uživatelé mimo organizaci) | Přijetí přihlašovacích údajů piv z jiných agentur

**ID**: NIST SP 800-53 R4 IA-8 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1347 – identifikace a ověřování (ne org). Uživatelé) \| přijetí přihlašovacích údajů piv. Z jiných Agys.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F131a2706-61e9-4916-a164-00e052056462) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1347.json) |

### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>Identifikace a ověřování (uživatelé mimo organizaci) | Přijetí přihlašovacích údajů třetích stran

**ID**: NIST SP 800-53 R4 IA-8 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1348 – identifikace a ověřování (ne org). Uživatelé) \| přijetí přihlašovacích údajů třetích stran](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F855ced56-417b-4d74-9d5f-dd1bc81e22d6) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1348.json) |

### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>Identifikace a ověřování (uživatelé mimo organizaci) | Používání produktů schválených Ficam

**ID**: NIST SP 800-53 R4 IA-8 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1349 – identifikace a ověřování (ne org). Uživatelé) \| používání produktů schválených FICAM](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17641f70-94cd-4a5d-a613-3d1143e20e34) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1349.json) |

### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>Identifikace a ověřování (uživatelé mimo organizaci) | Použití profilů vydaných Ficam

**ID**: NIST SP 800-53 R4 IA-8 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1350 – identifikace a ověřování (ne org). Uživatelé) \| použití profilů vydaných FICAM](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd77fd943-6ba6-4a21-ba07-22b03e347cc4) |Společnost Microsoft implementuje tento ovládací prvek identifikace a ověřování |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1350.json) |

## <a name="incident-response"></a>Reakce na incidenty

### <a name="incident-response-policy-and-procedures"></a>Zásady a postupy reakce na incidenty

**ID**: NIST SP 800-53 R4 IR-1 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1351 – zásady a postupy reakce na incidenty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbcfb6683-05e5-4ce6-9723-c3fbe9896bdd) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1351.json) |
|[Microsoft Managed Control 1352 – zásady a postupy reakce na incidenty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F518cb545-bfa8-43f8-a108-3b7d5037469a) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1352.json) |

### <a name="incident-response-training"></a>Školení reakcí na incidenty

**ID**: NIST SP 800-53 R4 IR-2 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1353 – školení reakcí na incidenty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc785ad59-f78f-44ad-9a7f-d1202318c748) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1353.json) |
|[Microsoft Managed Control 1354 – školení reakcí na incidenty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9fd92c17-163a-4511-bb96-bbb476449796) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1354.json) |
|[Microsoft Managed Control 1355 – školení reakcí na incidenty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90e01f69-3074-4de8-ade7-0fef3e7d83e0) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1355.json) |

### <a name="incident-response-training--simulated-events"></a>Školení reakcí na incidenty | Simulované události

**ID**: NIST SP 800-53 R4 IR-2 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1356 – simulované události školení pro incidenty \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8829f8f5-e8be-441e-85c9-85b72a5d0ef3) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1356.json) |

### <a name="incident-response-training--automated-training-environments"></a>Školení reakcí na incidenty | Automatizované školicí prostředí

**ID**: NIST SP 800-53 R4 IR-2 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1357 – školení reakcí na incidenty \| automatizované školicí prostředí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe4213689-05e8-4241-9d4e-8dd1cdafd105) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1357.json) |

### <a name="incident-response-testing"></a>Testování reakce na incidenty

**ID**: NIST SP 800-53 R4 IR-3 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1358 – testování reakce na incidenty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feffbaeef-5bf4-400d-895e-ef8cbc0e64c7) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1358.json) |

### <a name="incident-response-testing--coordination-with-related-plans"></a>Testování reakce na incidenty | Koordinace se souvisejícími plány

**ID**: NIST SP 800-53 R4 IR-3 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1359 – koordinace reakcí na incidenty \| se souvisejícími plány](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47bc7ea0-7d13-4f7c-a154-b903f7194253) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1359.json) |

### <a name="incident-handling"></a>Zpracování incidentů

**ID**: NIST SP 800-53 R4 IR-4 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1360 – zpracování incidentů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbe5b05e7-0b82-4ebc-9eda-25e447b1a41e) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1360.json) |
|[Spravovaný ovládací prvek Microsoft 1361 – zpracování incidentů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03ed3be1-7276-4452-9a5d-e4168565ac67) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1361.json) |
|[Spravovaný ovládací prvek Microsoft 1362 – zpracování incidentů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5d169442-d6ef-439b-8dca-46c2c3248214) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1362.json) |

### <a name="incident-handling--automated-incident-handling-processes"></a>Zpracování incidentů | Automatizované procesy zpracování incidentů

**ID**: NIST SP 800-53 R4 IR-4 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1363 – \| procesy automatizovaného zpracování incidentů zpracovávající incidenty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea3e8156-89a1-45b1-8bd6-938abc79fdfd) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1363.json) |

### <a name="incident-handling--dynamic-reconfiguration"></a>Zpracování incidentů | Dynamická rekonfigurace

**ID**: NIST SP 800-53 R4 IR-4 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1364 – \| Dynamická rekonfigurace zpracování incidentů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c615c2a-dc83-4dda-8220-abce7b50c9bc) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1364.json) |

### <a name="incident-handling--continuity-of-operations"></a>Zpracování incidentů | Kontinuita operací

**ID**: NIST SP 800-53 R4 IR-4 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1365 – \| provozní kontinuita zpracování incidentů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4116891d-72f7-46ee-911c-8056cc8dcbd5) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1365.json) |

### <a name="incident-handling--information-correlation"></a>Zpracování incidentů | Korelace informací

**ID**: NIST SP 800-53 R4 IR-4 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1366 – \| korelace informací o zpracování incidentů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06c45c30-ae44-4f0f-82be-41331da911cc) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1366.json) |

### <a name="incident-handling--insider-threats---specific-capabilities"></a>Zpracování incidentů | Možnosti specifické pro hrozby Insider

**ID**: NIST SP 800-53 R4 IR-4 (6) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1367 – \| možnosti specifické pro hrozby Insider](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F435b2547-6374-4f87-b42d-6e8dbe6ae62a) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1367.json) |

### <a name="incident-handling--correlation-with-external-organizations"></a>Zpracování incidentů | Korelace s externími organizacemi

**ID**: NIST SP 800-53 R4 IR-4 (8) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1368 – korelace zpracování incidentů \| s externími organizacemi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F465f32da-0ace-4603-8d1b-7be5a3a702de) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1368.json) |

### <a name="incident-monitoring"></a>Monitorování incidentů

**ID**: NIST SP 800-53 R4 IR-5 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1369 – sledování incidentů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18cc35ed-a429-486d-8d59-cb47e87304ed) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1369.json) |

### <a name="incident-monitoring--automated-tracking--data-collection--analysis"></a>Monitorování incidentu | Automatizované sledování/shromažďování a analýza dat

**ID**: NIST SP 800-53 R4 IR-5 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1370 – \| automatizované sledování sledování incidentů/shromažďování a analýza dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F924e1b2d-c502-478f-bfdb-a7e09a0d5c01) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1370.json) |

### <a name="incident-reporting"></a>Generování sestav incidentů

**ID**: NIST SP 800-53 R4 IR-6 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1371 – generování sestav incidentů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9447f354-2c85-4700-93b3-ecdc6cb6a417) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1371.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1372 – generování sestav incidentů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F25b96717-c912-4c00-9143-4e487f411726) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1372.json) |

### <a name="incident-reporting--automated-reporting"></a>Generování sestav incidentu | Automatizované vytváření sestav

**ID**: NIST SP 800-53 R4 IR-6 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1373 – generování sestav incidentů \| automatizované vytváření sestav](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4cca950f-c3b7-492a-8e8f-ea39663c14f9) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1373.json) |

### <a name="incident-response-assistance"></a>Pomoc při reakci na incidenty

**ID**: NIST SP 800-53 R4 IR-7 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1374 – pomoc při reakci na incidenty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc5c8616-52ef-4e5e-8000-491634ed9249) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1374.json) |

### <a name="incident-response-assistance--automation-support-for-availability-of-information--support"></a>Pomoc při reakci na incidenty | Podpora automatizace dostupnosti informací/podpory

**ID**: NIST SP 800-53 R4 IR-7 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1375 – podpora pro reakci na incidenty \| automatizace pro dostupnost informací/podpory](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F00379355-8932-4b52-b63a-3bc6daf3451a) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1375.json) |

### <a name="incident-response-assistance--coordination-with-external-providers"></a>Pomoc při reakci na incidenty | Koordinace s externími poskytovateli

**ID**: NIST SP 800-53 R4 IR-7 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1376 – koordinace pomocných odpovědí na incidenty \| s externími poskytovateli](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F493a95f3-f2e3-47d0-af02-65e6d6decc2f) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1376.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1377 – koordinace pomocných odpovědí na incidenty \| s externími poskytovateli](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68434bd1-e14b-4031-9edb-a4adf5f84a67) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1377.json) |

### <a name="incident-response-plan"></a>Plán reakce na incidenty

**ID**: NIST SP 800-53 R4 IR-8 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1378 – plán reakce na incidenty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F97fceb70-6983-42d0-9331-18ad8253184d) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1378.json) |
|[Microsoft Managed Control 1379 – plán reakce na incidenty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9442dd2c-a07f-46cd-b55a-553b66ba47ca) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1379.json) |
|[Microsoft Managed Control 1380 – plán reakce na incidenty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4319b7e-ea8d-42ff-8a67-ccd462972827) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1380.json) |
|[Microsoft Managed Control 1381 – plán reakce na incidenty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe5368258-9684-4567-8126-269f34e65eab) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1381.json) |
|[Microsoft Managed Control 1382 – plán reakce na incidenty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F841392b3-40da-4473-b328-4cde49db67b3) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1382.json) |
|[Microsoft Managed Control 1383 – plán reakce na incidenty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd4558451-e16a-4d2d-a066-fe12a6282bb9) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1383.json) |

### <a name="information-spillage-response"></a>Odezva na únik informací

**ID**: NIST SP 800-53 R4 IR-9 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1384 – odpověď na informační úniky](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F79fbc228-461c-4a45-9004-a865ca0728a7) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1384.json) |
|[Microsoft Managed Control 1385 – odpověď na informační úniky](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3e495e65-8663-49ca-9b38-9f45e800bc58) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1385.json) |
|[Microsoft Managed Control 1386 – odpověď na informační úniky](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5120193e-91fd-4f9d-bc6d-194f94734065) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1386.json) |
|[Microsoft Managed Control 1387 – odpověď na informační úniky](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3007185-3857-43a9-8237-06ca94f1084c) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1387.json) |
|[Microsoft Managed Control 1388 – odpověď na informační úniky](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c7c575a-d4c5-4f6f-bd49-dee97a8cba55) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1388.json) |
|[Microsoft Managed Control 1389 – odpověď na informační úniky](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39e6fda-ae70-4891-a739-be7bba6d1062) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1389.json) |

### <a name="information-spillage-response--responsible-personnel"></a>Odpověď na únik informací | Odpovědní pracovníci

**ID**: NIST SP 800-53 R4 IR-9 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1390 – informační úniky odpovědí \| zodpovědné pracovníky](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3b65b63-09ec-4cb5-8028-7dd324d10eb0) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1390.json) |

### <a name="information-spillage-response--training"></a>Odpověď na únik informací | Absolv

**ID**: NIST SP 800-53 R4 IR-9 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1391 – školení odpovědí na informační úniky \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd6ac1a1-660e-4810-baa8-74e868e2ed47) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1391.json) |

### <a name="information-spillage-response--post-spill-operations"></a>Odpověď na únik informací | Operace po obdata

**ID**: NIST SP 800-53 R4 IR-9 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1392 – informační úniky odpovědi na \| operace po přelití](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86dc819f-15e1-43f9-a271-41ae58d4cecc) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1392.json) |

### <a name="information-spillage-response--exposure-to-unauthorized-personnel"></a>Odpověď na únik informací | Ozáření pro neoprávněné zaměstnance

**ID**: NIST SP 800-53 R4 IR-9 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1393 – odezva \| na únik informací pro neoprávněné zaměstnance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F731856d8-1598-4b75-92de-7d46235747c0) |Společnost Microsoft implementuje tento ovládací prvek reakce na incidenty |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1393.json) |

## <a name="maintenance"></a>Údržba

### <a name="system-maintenance-policy-and-procedures"></a>Zásady údržby systému a postupy

**ID**: NIST SP 800-53 R4 mA-1 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1394 – zásady a postupy údržby systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4db56f68-3f50-45ab-88f3-ca46f5379a94) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1394.json) |
|[Microsoft Managed Control 1395 – zásady a postupy údržby systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7207a023-a517-41c5-9df2-09d4c6845a05) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1395.json) |

### <a name="controlled-maintenance"></a>Řízená údržba

**ID**: NIST SP 800-53 R4 ma-2 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Údržba řízená přes Microsoft Managed Control 1396](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F276af98f-4ff9-4e69-99fb-c9b2452fb85f) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1396.json) |
|[Údržba řízená přes Microsoft Managed Control 1397](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F391af4ab-1117-46b9-b2c7-78bbd5cd995b) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1397.json) |
|[Údržba řízená přes Microsoft Managed Control 1398](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F443e8f3d-b51a-45d8-95a7-18b0e42f4dc4) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1398.json) |
|[Údržba řízená přes Microsoft Managed Control 1399](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2256e638-eb23-480f-9e15-6cf1af0a76b3) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1399.json) |
|[Údržba řízená přes Microsoft Managed Control 1400](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa96d5098-a604-4cdf-90b1-ef6449a27424) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1400.json) |
|[Údržba řízená přes Microsoft Managed Control 1401](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb78ee928-e3c1-4569-ad97-9f8c4b629847) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1401.json) |

### <a name="controlled-maintenance--automated-maintenance-activities"></a>Řízená údržba | Automatizované aktivity údržby

**ID**: NIST SP 800-53 R4 ma-2 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1402 – automatizované aktivity údržby řízené údržby \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a560d32-8075-4fec-9615-9f7c853f4ea9) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1402.json) |
|[Microsoft Managed Control 1403 – automatizované aktivity údržby řízené údržby \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F57149289-d52b-4f40-9fe6-5233c1ef80f7) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1403.json) |

### <a name="maintenance-tools"></a>Nástroje údržby

**ID**: NIST SP 800-53 R4 ma-3 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1404 – nástroje údržby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13d8f903-0cd6-449f-a172-50f6579c182b) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1404.json) |

### <a name="maintenance-tools--inspect-tools"></a>Nástroje pro údržbu | Kontrola nástrojů

**ID**: NIST SP 800-53 R4 ma-3 (1) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1405-nástroje pro údržbu \| kontroly nástrojů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe1a0bf3-409a-4b00-b60d-0b1f917f7e7b) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1405.json) |

### <a name="maintenance-tools--inspect-media"></a>Nástroje pro údržbu | Kontrola multimédií

**ID**: NIST SP 800-53 R4 ma-3 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1406 – nástroje údržby \| Kontrola médií](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0f5339c-9292-43aa-a0bc-d27c6b8e30aa) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1406.json) |

### <a name="maintenance-tools--prevent-unauthorized-removal"></a>Nástroje pro údržbu | Zabránit neoprávněnému odebrání

**ID**: NIST SP 800-53 R4 ma-3 (3) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1407 – nástroje pro údržbu \| zabraňují neoprávněnému odebrání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fff9fbd83-1d8d-4b41-aac2-94cb44b33976) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1407.json) |
|[Microsoft Managed Control 1408 – nástroje pro údržbu \| zabraňují neoprávněnému odebrání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5f56ac6-4bb2-4086-bc41-ad76344ba2c2) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1408.json) |
|[Microsoft Managed Control 1409 – nástroje pro údržbu \| zabraňují neoprávněnému odebrání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd1880188-e51a-4772-b2ab-68f5e8bd27f6) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1409.json) |
|[Microsoft Managed Control 1410 – nástroje pro údržbu \| zabraňují neoprávněnému odebrání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2596a9f-e59f-420d-9625-6e0b536348be) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1410.json) |

### <a name="nonlocal-maintenance"></a>Nemístní údržba

**ID**: NIST SP 800-53 R4 mA-4 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1411 – nemístní údržba](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F898d4fe8-f743-4333-86b7-0c9245d93e7d) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1411.json) |
|[Microsoft Managed Control 1412 – nemístní údržba](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3492d949-0dbb-4589-88b3-7b59601cc764) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1412.json) |
|[Microsoft Managed Control 1413 – nemístní údržba](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faeedddb6-6bc0-42d5-809b-80048033419d) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1413.json) |
|[Microsoft Managed Control 1414 – nemístní údržba](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ce63a52-e47b-4ae2-adbb-6e40d967f9e6) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1414.json) |
|[Microsoft Managed Control 1415 – nemístní údržba](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F61a1dd98-b259-4840-abd5-fbba7ee0da83) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1415.json) |

### <a name="nonlocal-maintenance--document-nonlocal-maintenance"></a>Nemístní údržba | Dokumentovat nemístní údržbu

**ID**: NIST SP 800-53 R4 mA-4 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1416 – nemístní \| Údržba dokumentu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F38dfd8a3-5290-4099-88b7-4081f4c4d8ae) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1416.json) |

### <a name="nonlocal-maintenance--comparable-security--sanitization"></a>Nemístní údržba | Srovnatelné zabezpečení/upravení

**ID**: NIST SP 800-53 R4 mA-4 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1417 – nemístní údržba \| srovnatelné se zabezpečením/upravením](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7522ed84-70d5-4181-afc0-21e50b1b6d0e) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1417.json) |
|[Microsoft Managed Control 1418 – nemístní údržba \| srovnatelné se zabezpečením/upravením](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28e633fd-284e-4ea7-88b4-02ca157ed713) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1418.json) |

### <a name="nonlocal-maintenance--cryptographic-protection"></a>Nemístní údržba | Kryptografická ochrana

**ID**: NIST SP 800-53 R4 mA-4 (6) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1419 – \| kryptografická ochrana nemístní údržby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6747bf9-2b97-45b8-b162-3c8becb9937d) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1419.json) |

### <a name="maintenance-personnel"></a>Pracovníci údržby

**ID**: NIST SP 800-53 R4 ma-5 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1420 – zaměstnanci údržby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05ae08cc-a282-413b-90c7-21a2c60b8404) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1420.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1421 – zaměstnanci údržby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe539caaa-da8c-41b8-9e1e-449851e2f7a6) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1421.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1422 – zaměstnanci údržby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea556850-838d-4a37-8ce5-9d7642f95e11) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1422.json) |

### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Pracovníci údržby | Jednotlivci bez odpovídajícího přístupu

**ID**: NIST SP 800-53 R4 ma-5 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1423 – zaměstnanci údržby \| bez odpovídajícího přístupu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7741669e-d4f6-485a-83cb-e70ce7cbbc20) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1423.json) |
|[Microsoft Managed Control 1424 – zaměstnanci údržby \| bez odpovídajícího přístupu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf55fc87-48e1-4676-a2f8-d9a8cf993283) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1424.json) |

### <a name="timely-maintenance"></a>Včasná údržba

**ID**: NIST SP 800-53 R4 MA-6 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1425 – včasná údržba](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5983d99c-f39b-4c32-a3dc-170f19f6941b) |Microsoft implementuje tuto kontrolu údržby. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1425.json) |

## <a name="media-protection"></a>Ochrana médií

### <a name="media-protection-policy-and-procedures"></a>Zásady a procedury ochrany médií

**ID**: NIST SP 800-53 R4 MP-1 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1426 – zásady a postupy ochrany médií](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21f639bc-f42b-46b1-8f40-7a2a389c291a) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1426.json) |
|[Microsoft Managed Control 1427 – zásady a postupy ochrany médií](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc90e44f-d83f-4bdf-900f-3d5eb4111b31) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1427.json) |

### <a name="media-access"></a>Přístup k médiím

**ID**: NIST SP 800-53 R4 MP-2 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1428 – přístup k médiím](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a77fcc7-b8d8-451a-ab52-56197913c0c7) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1428.json) |

### <a name="media-marking"></a>Označení média

**ID**: NIST SP 800-53 R4 MP-3 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1429 – označení média](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb07c9b24-729e-4e85-95fc-f224d2d08a80) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1429.json) |
|[Spravovaný ovládací prvek Microsoft 1430 – označení média](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f559588-5e53-4b14-a7c4-85d28ebc2234) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1430.json) |

### <a name="media-storage"></a>Mediální úložiště

**ID**: NIST SP 800-53 R4 MP-4 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1431 – Storage Media](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7173c52-2b99-4696-a576-63dd5f970ef4) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1431.json) |
|[Microsoft Managed Control 1432 – Storage Media](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1140e542-b80d-4048-af45-3f7245be274b) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1432.json) |

### <a name="media-transport"></a>Media Transport

**ID**: NIST SP 800-53 R4 MP-5 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1433-Media Transport](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b879b41-2728-41c5-ad24-9ee2c37cbe65) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1433.json) |
|[Microsoft Managed Control 1434-Media Transport](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c18f06b-a68d-41c3-8863-b8cd3acb5f8f) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1434.json) |
|[Microsoft Managed Control 1435-Media Transport](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa8d221b-d130-4637-ba16-501e666628bb) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1435.json) |
|[Microsoft Managed Control 1436-Media Transport](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28aab8b4-74fd-4b7c-9080-5a7be525d574) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1436.json) |

### <a name="media-transport--cryptographic-protection"></a>Přenos médií | Kryptografická ochrana

**ID**: NIST SP 800-53 R4 MP-5 (4) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1437 – \| kryptografická ochrana Media Transport](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d1eb6ed-bf13-4046-b993-b9e2aef0f76c) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1437.json) |

### <a name="media-sanitization"></a>Upravení médií

**ID**: NIST SP 800-53 R4 MP-6 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1438 – Správa média](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40fcc635-52a2-4dbc-9523-80a1f4aa1de6) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1438.json) |
|[Microsoft Managed Control 1439 – Správa média](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdce72873-c5f1-47c3-9b4f-6b8207fd5a45) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1439.json) |

### <a name="media-sanitization--review--approve--track--document--verify"></a>Upravení médií | Zkontrolovat/schválit/sledovat/dokumentovat/ověřit

**ID**: NIST SP 800-53 R4 MP-6 (1) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1440- \| Kontrola/schválení média/sledování/dokument/ověření](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F881299bf-2a5b-4686-a1b2-321d33679953) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1440.json) |

### <a name="media-sanitization--equipment-testing"></a>Upravení médií | Testování zařízení

**ID**: NIST SP 800-53 R4 MP-6 (2) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1441 – testování vybavení pro úpravu média \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6519d7f3-e8a2-4ff3-a935-9a9497152ad7) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1441.json) |

### <a name="media-sanitization--nondestructive-techniques"></a>Upravení médií | Nedestruktivní techniky

**ID**: NIST SP 800-53 R4 MP-6 (3) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1442 – \| nedestruktivní postupy pro úpravu média](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f26049b-2c5a-4841-9ff3-d48a26aae475) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1442.json) |

### <a name="media-use"></a>Použití médií

**ID**: NIST SP 800-53 R4 MP-7 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1443 – použití multimédií](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcd0ec6fa-a2e7-4361-aee4-a8688659a9ed) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1443.json) |

### <a name="media-use--prohibit-use-without-owner"></a>Použití média | Zakázat použití bez vlastníka

**ID**: NIST SP 800-53 R4 MP-7 (1) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1444 – použití média \| zakázat použití bez vlastníka](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F666143df-f5e0-45bd-b554-135f0f93e44e) |Microsoft implementuje tuto kontrolu Media Protection. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1444.json) |

## <a name="physical-and-environmental-protection"></a>Ochrana fyzických a environmentálních prostředí

### <a name="physical-and-environmental-protection-policy-and-procedures"></a>Zásady a postupy pro fyzickou a environmentální ochranu

**ID**: NIST SP 800-53 R4 PE-1 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1445 – zásady a postupy ochrany životního prostředí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32d07d59-2716-4972-b37b-214a67ac4a37) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1445.json) |
|[Microsoft Managed Control 1446 – zásady a postupy ochrany životního prostředí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf6850fe-abba-468e-9ef4-d09ec7d983cd) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1446.json) |

### <a name="physical-access-authorizations"></a>Autorizace fyzického přístupu

**ID**: NIST SP 800-53 R4 PE-2 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1447 – autorizace fyzického přístupu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9783a99-98fe-4a95-873f-29613309fe9a) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1447.json) |
|[Microsoft Managed Control 1448 – autorizace fyzického přístupu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F825d6494-e583-42f2-a3f2-6458e6f0004f) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1448.json) |
|[Microsoft Managed Control 1449 – autorizace fyzického přístupu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff784d3b0-5f2b-49b7-b9f3-00ba8653ced5) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1449.json) |
|[Microsoft Managed Control 1450 – autorizace fyzického přístupu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F134d7a13-ba3e-41e2-b236-91bfcfa24e01) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1450.json) |

### <a name="physical-access-control"></a>Fyzický Access Control

**ID**: NIST SP 800-53 R4 PE-3 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1451 – fyzický Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3f1e5a3-25c1-4476-8cb6-3955031f8e65) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1451.json) |
|[Microsoft Managed Control 1452 – fyzický Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82c76455-4d3f-4e09-a654-22e592107e74) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1452.json) |
|[Microsoft Managed Control 1453 – fyzický Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9693b564-3008-42bc-9d5d-9c7fe198c011) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1453.json) |
|[Microsoft Managed Control 1454 – fyzický Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad58985d-ab32-4f99-8bd3-b7e134c90229) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1454.json) |
|[Microsoft Managed Control 1455 – fyzický Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F068a88d4-e520-434e-baf0-9005a8164e6a) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1455.json) |
|[Microsoft Managed Control 1456 – fyzický Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F733ba9e3-9e7c-440a-a7aa-6196a90a2870) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1456.json) |
|[Microsoft Managed Control 1457 – fyzický Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff2d9d3e6-8886-4305-865d-639163e5c305) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1457.json) |

### <a name="physical-access-control--information-system-access"></a>Fyzický Access Control | Přístup k informačnímu systému

**ID**: NIST SP 800-53 R4 PE-3 (1) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1458 – \| přístup k informacím o fyzickém Access Controlm systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c19ceb7-56e9-4488-8ddb-b1eb3aa6d203) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1458.json) |

### <a name="access-control-for-transmission-medium"></a>Access Control pro přenosové médium

**ID**: NIST SP 800-53 R4 PE-4 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1459-Access Control pro přenosové médium](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F75cc73c7-5cdb-479d-a06f-7b4d0dbb1da0) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1459.json) |

### <a name="access-control-for-output-devices"></a>Access Control pro výstupní zařízení

**ID**: NIST SP 800-53 R4 PE-5 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1460-Access Control pro výstupní zařízení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6f3ce1bb-4f77-4695-8355-70b08d54fdda) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1460.json) |

### <a name="monitoring-physical-access"></a>Monitorování fyzického přístupu

**ID**: NIST SP 800-53 R4 PE-6 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1461 – monitorování fyzického přístupu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faafef03e-fea8-470b-88fa-54bd1fcd7064) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1461.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1462 – monitorování fyzického přístupu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9b1f3a9a-13a1-4b40-8420-36bca6fd8c02) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1462.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1463 – monitorování fyzického přístupu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F59721f87-ae25-4db0-a2a4-77cc5b25d495) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1463.json) |

### <a name="monitoring-physical-access--intrusion-alarms--surveillance-equipment"></a>Monitorování fyzického přístupu | Alarmy a kontrolní zařízení pro vniknutí

**ID**: NIST SP 800-53 R4 PE-6 (1) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1464 – \| Upozornění na neoprávněné vniknutí a sledovací zařízení pro fyzický přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41256567-1795-4684-b00b-a1308ce43cac) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1464.json) |

### <a name="monitoring-physical-access--monitoring-physical-access-to-information-systems"></a>Monitorování fyzického přístupu | Monitorování fyzického přístupu k informačním systémům

**ID**: NIST SP 800-53 R4 PE-6 (4) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1465 – monitorování fyzického přístupu \| monitorování fyzického přístupu k informačním systémům](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6e41554-86b5-4537-9f7f-4fc41a1d1640) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1465.json) |

### <a name="visitor-access-records"></a>Záznamy o přístupu návštěvníků

**ID**: NIST SP 800-53 R4 PE-8 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravované řízení společnosti Microsoft 1466 – záznamy přístupu návštěvníků](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d943a9c-a6f1-401f-a792-740cdb09c451) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1466.json) |
|[Spravované řízení společnosti Microsoft 1467 – záznamy přístupu návštěvníků](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5350cbf9-8bdd-4904-b22a-e88be84ca49d) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1467.json) |

### <a name="visitor-access-records--automated-records-maintenance--review"></a>Záznamy přístupu návštěvníků | Údržba a kontrola automatizovaných záznamů

**ID**: NIST SP 800-53 R4 PE-8 (1) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravované řízení společnosti Microsoft 1468 – evidence \| automatických záznamů a kontrola přístupu návštěvníků](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F75603f96-80a1-4757-991d-5a1221765ddd) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1468.json) |

### <a name="power-equipment-and-cabling"></a>Napájecí zařízení a kabeláž

**ID**: NIST SP 800-53 R4 PE-9 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1469-Power Equipment and kabeláže](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff509c5b6-0de0-4a4e-9b2e-cd9cbf3a58fd) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1469.json) |

### <a name="emergency-shutoff"></a>Nouzový shutoff

**ID**: NIST SP 800-53 R4 PE-10 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1470 – Emergency shutoff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc89ba09f-2e0f-44d0-8095-65b05bd151ef) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1470.json) |
|[Microsoft Managed Control 1471 – Emergency shutoff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7dd0e9ce-1772-41fb-a50a-99977071f916) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1471.json) |
|[Microsoft Managed Control 1472 – Emergency shutoff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef869332-921d-4c28-9402-3be73e6e50c8) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1472.json) |

### <a name="emergency-power"></a>Nouzový výkon

**ID**: NIST SP 800-53 R4 PE-11 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1473 – nouzový výkon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd7047705-d719-46a7-8bb0-76ad233eba71) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1473.json) |

### <a name="emergency-power--long-term-alternate-power-supply---minimal-operational-capability"></a>Nouzový výkon | Dlouhodobý alternativní zdroj napájení – minimální provozní schopnost

**ID**: NIST SP 800-53 R4 PE-11 (1) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1474 – nouzový výkon dlouhodobě \| alternativním zdrojem napájení – minimální provozní schopnost](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03ad326e-d7a1-44b1-9a76-e17492efc9e4) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1474.json) |

### <a name="emergency-lighting"></a>Nouzové osvětlení

**ID**: NIST SP 800-53 R4 PE-12 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1475 – nouzové osvětlení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34a63848-30cf-4081-937e-ce1a1c885501) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1475.json) |

### <a name="fire-protection"></a>Požární ochrana

**ID**: NIST SP 800-53 R4 PE-13 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1476 – požární ochrana](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f3c4ac2-3e35-4906-a80b-473b12a622d7) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1476.json) |

### <a name="fire-protection--detection-devices--systems"></a>Protipožární ochrana | Detekce zařízení/systémů

**ID**: NIST SP 800-53 R4 PE-13 (1) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1477 – \| zařízení/systémy zjišťování ochrany před požáry](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4862a63c-6c74-4a9d-a221-89af3c374503) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1477.json) |

### <a name="fire-protection--suppression-devices--systems"></a>Protipožární ochrana | Zařízení/systémy potlačení

**ID**: NIST SP 800-53 R4 PE-13 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1478 – \| zařízení/systémy potlačení brány fire Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff997df46-cfbb-4cc8-aac8-3fecdaf6a183) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1478.json) |

### <a name="fire-protection--automatic-fire-suppression"></a>Protipožární ochrana | Automatické potlačení požáru

**ID**: NIST SP 800-53 R4 PE-13 (3) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1479 – \| automatické potlačení požární ochrany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe327b072-281d-4f75-9c28-4216e5d72f26) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1479.json) |

### <a name="temperature-and-humidity-controls"></a>Kontroly teploty a vlhkosti

**ID**: NIST SP 800-53 R4 PE-14 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1480 – kontroly teploty a vlhkosti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18a767cc-1947-4338-a240-bc058c81164f) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1480.json) |
|[Microsoft Managed Control 1481 – kontroly teploty a vlhkosti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F717a1c78-a267-4f56-ac58-ee6c54dc4339) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1481.json) |

### <a name="temperature-and-humidity-controls--monitoring-with-alarms--notifications"></a>Řízení teploty a vlhkosti | Monitorování pomocí alarmů a oznámení

**ID**: NIST SP 800-53 R4 PE-14 (2) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1482 – řízení teploty a vlhkosti \| monitoruje výstrahy a oznámení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9df4277e-8c88-4d5c-9b1a-541d53d15d7b) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1482.json) |

### <a name="water-damage-protection"></a>Ochrana před poškozením vody

**ID**: NIST SP 800-53 R4 PE-15 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1483 – ochrana před poškozením vody](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5cb81060-3c8a-4968-bcdc-395a1801f6c1) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1483.json) |

### <a name="water-damage-protection--automation-support"></a>Ochrana proti poškození vod | Podpora automatizace

**ID**: NIST SP 800-53 R4 PE-15 (1) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1484 – Podpora automatizace ochrany před poškozením vody \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F486b006a-3653-45e8-b41c-a052d3e05456) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1484.json) |

### <a name="delivery-and-removal"></a>Doručení a odebrání

**ID**: NIST SP 800-53 R4 PE-16 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1485 – doručování a odebírání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50301354-95d0-4a11-8af5-8039ecf6d38b) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1485.json) |

### <a name="alternate-work-site"></a>Alternativní pracovní web

**ID**: NIST SP 800-53 R4 PE-17 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1486 – alternativní pracovní web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb790345-a51f-43de-934e-98dbfaf9dca5) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1486.json) |
|[Microsoft Managed Control 1487 – alternativní pracovní web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c3371d-c30c-4f58-abd9-30b8a8199571) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1487.json) |
|[Microsoft Managed Control 1488 – alternativní pracovní web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd8ef30eb-a44f-47af-8524-ac19a36d41d2) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1488.json) |

### <a name="location-of-information-system-components"></a>Umístění součástí informačního systému

**ID**: NIST SP 800-53 R4 PE-18 **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1489 – umístění součástí informačního systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0a794f-1444-4c96-9534-e35fc8c39c91) |Společnost Microsoft implementuje toto fyzické a environmentální řízení ochrany. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1489.json) |

## <a name="planning"></a>Plánování

### <a name="security-planning-policy-and-procedures"></a>Zásady a postupy plánování zabezpečení

**ID**: NIST SP 800-53 R4 pl-1 – **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1490 – zásady a postupy plánování zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e61da80-0957-4892-b70c-609d5eaafb6b) |Společnost Microsoft implementuje tento ovládací prvek plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1490.json) |
|[Microsoft Managed Control 1491 – zásady a postupy plánování zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1571dd40-dafc-4ef4-8f55-16eba27efc7b) |Společnost Microsoft implementuje tento ovládací prvek plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1491.json) |

### <a name="system-security-plan"></a>Plán zabezpečení systému

**ID**: NIST SP 800-53 R4 pl-2 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1492 – plán zabezpečení systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ad5f307-e045-46f7-8214-5bdb7e973737) |Společnost Microsoft implementuje tento ovládací prvek plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1492.json) |
|[Microsoft Managed Control 1493 – plán zabezpečení systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22b469b3-fccf-42da-aa3b-a28e6fb113ce) |Společnost Microsoft implementuje tento ovládací prvek plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1493.json) |
|[Microsoft Managed Control 1494 – plán zabezpečení systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ed09d84-3311-4853-8b67-2b55dfa33d09) |Společnost Microsoft implementuje tento ovládací prvek plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1494.json) |
|[Microsoft Managed Control 1495 – plán zabezpečení systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4978d0e-a596-48e7-9f8c-bbf52554ce8d) |Společnost Microsoft implementuje tento ovládací prvek plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1495.json) |
|[Microsoft Managed Control 1496 – plán zabezpečení systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ca96127-2f87-46ab-a4fc-0d2a786df1c8) |Společnost Microsoft implementuje tento ovládací prvek plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1496.json) |

### <a name="system-security-plan--plan--coordinate-with-other-organizational-entities"></a>Plán zabezpečení systému | Plánování a koordinace s ostatními organizačními entitami

**ID**: NIST SP 800-53 R4 pl-2 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1497-plán \| /koordinace zabezpečení systému pomocí dalších organizačních entit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e3c5583-1729-4d36-8771-59c32f090a22) |Společnost Microsoft implementuje tento ovládací prvek plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1497.json) |

### <a name="rules-of-behavior"></a>Pravidla chování

**ID**: NIST SP 800-53 R4 pl-4 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1498 – pravidla chování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F633988b9-cf2f-4323-8394-f0d2af9cd6e1) |Společnost Microsoft implementuje tento ovládací prvek plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1498.json) |
|[Microsoft Managed Control 1499 – pravidla chování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe59671ab-9720-4ee2-9c60-170e8c82251e) |Společnost Microsoft implementuje tento ovládací prvek plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1499.json) |
|[Microsoft Managed Control 1500 – pravidla chování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9dd5b241-03cb-47d3-a5cd-4b89f9c53c92) |Společnost Microsoft implementuje tento ovládací prvek plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1500.json) |
|[Microsoft Managed Control 1501 – pravidla chování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88817b58-8472-4f6c-81fa-58ce42b67f51) |Společnost Microsoft implementuje tento ovládací prvek plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1501.json) |

### <a name="rules-of-behavior--social-media-and-networking-restrictions"></a>Pravidla chování | Síťová omezení pro sociální média a sítě

**ID**: NIST SP 800-53 R4 pl-4 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1502 – pravidla chování \| sociálních médií a síťových omezení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe901375c-8f01-4ac8-9183-d5312f47fe63) |Společnost Microsoft implementuje tento ovládací prvek plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1502.json) |

### <a name="information-security-architecture"></a>Architektura zabezpečení informací

**ID**: NIST SP 800-53 R4 pl-8 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1503 – Architektura zabezpečení informací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc1fa9c2f-d439-4ab9-8b83-81fb1934f81d) |Společnost Microsoft implementuje tento ovládací prvek plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1503.json) |
|[Microsoft Managed Control 1504 – Architektura zabezpečení informací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e7c35d0-12d4-4e0c-80a2-8a352537aefd) |Společnost Microsoft implementuje tento ovládací prvek plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1504.json) |
|[Microsoft Managed Control 1505 – Architektura zabezpečení informací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F813a10a7-3943-4fe3-8678-00dc52db5490) |Společnost Microsoft implementuje tento ovládací prvek plánování. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1505.json) |

## <a name="personnel-security"></a>Zabezpečení pracovníků

### <a name="personnel-security-policy-and-procedures"></a>Zásady a postupy zabezpečení zaměstnanců

**ID**: NIST SP 800-53 R4 PS-1 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1506 – zásady a postupy zabezpečení zaměstnanců](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d2ff17-d604-4dd9-b607-9ecf63f28ad2) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1506.json) |
|[Microsoft Managed Control 1507 – zásady a postupy zabezpečení zaměstnanců](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86ccd1bf-e7ad-4851-93ce-6ec817469c1e) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1507.json) |

### <a name="position-risk-designation"></a>Označení rizika pozice

**ID**: NIST SP 800-53 R4 PS-2 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1508 – označení rizika pozice](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F76f500cc-4bca-4583-bda1-6d084dc21086) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1508.json) |
|[Microsoft Managed Control 1509 – označení rizika pozice](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F70792197-9bfc-4813-905a-bd33993e327f) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1509.json) |
|[Microsoft Managed Control 1510 – označení rizika pozice](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F79da5b09-0e7e-499e-adda-141b069c7998) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1510.json) |

### <a name="personnel-screening"></a>Blokování pracovníků

**ID**: NIST SP 800-53 R4 PS-3 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1511 – blokování zaměstnanců](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9eae324-d327-4539-9293-b48e122465f8) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1511.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1512 – blokování zaměstnanců](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5a8324ad-f599-429b-aaed-f9c6e8c987a8) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1512.json) |

### <a name="personnel-screening--information-with-special-protection-measures"></a>Blokování pracovníků | Informace s opatřeními zvláštní ochrany

**ID**: NIST SP 800-53 R4 PS-3 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1513 – \| informace o kontrole personálu se speciálními ochrannými opatřeními](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc416970d-b12b-49eb-8af4-fb144cd7c290) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1513.json) |
|[Microsoft Managed Control 1514 – \| informace o kontrole personálu se speciálními ochrannými opatřeními](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ed5ca00-0e43-434e-a018-7aab91461ba7) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1514.json) |

### <a name="personnel-termination"></a>Ukončení pracovníků

**ID**: NIST SP 800-53 R4 PS-4 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1515 – ukončení zaměstnanců](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02dd141a-a2b2-49a7-bcbd-ca31142f6211) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1515.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1516 – ukončení zaměstnanců](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda3cd269-156f-435b-b472-c3af34c032ed) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1516.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1517 – ukončení zaměstnanců](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8f5ad423-50d6-4617-b058-69908f5586c9) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1517.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1518 – ukončení zaměstnanců](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d58f734-c052-40e9-8b2f-a1c2bff0b815) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1518.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1519 – ukončení zaměstnanců](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f13915a-324c-4ab8-b45c-2eefeeefb098) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1519.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1520 – ukončení zaměstnanců](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f2c513b-eb16-463b-b469-c10e5fa94f0a) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1520.json) |

### <a name="personnel-termination--automated-notification"></a>Ukončení pracovníků | Automatizované oznámení

**ID**: NIST SP 800-53 R4 PS-4 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1521 – \| automatizované oznámení o ukončení zaměstnanců](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cbddf9c-a3aa-4330-a0f5-4c0c1f1862e5) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1521.json) |

### <a name="personnel-transfer"></a>Osobní přenos

**ID**: NIST SP 800-53 R4 PS-5 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1522 – přenos personálu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F38b470cc-f939-4a15-80e0-9f0c74f2e2c9) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1522.json) |
|[Microsoft Managed Control 1523 – přenos personálu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5577a310-2551-49c8-803b-36e0d5e55601) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1523.json) |
|[Microsoft Managed Control 1524 – přenos personálu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72f1cb4e-2439-4fe8-88ea-b8671ce3c268) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1524.json) |
|[Microsoft Managed Control 1525 – přenos personálu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9be2f688-7a61-45e3-8230-e1ec93893f66) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1525.json) |

### <a name="access-agreements"></a>Dohody o přístupu

**ID**: NIST SP 800-53 R4 PS-6 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1526 – dohody o přístupu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F953e6261-a05a-44fd-8246-000e1a3edbb9) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1526.json) |
|[Microsoft Managed Control 1527 – dohody o přístupu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2823de66-332f-4bfd-94a3-3eb036cd3b67) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1527.json) |
|[Microsoft Managed Control 1528 – dohody o přístupu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdeb9797c-22f8-40e8-b342-a84003c924e6) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1528.json) |

### <a name="third-party-personnel-security"></a>Zabezpečení pracovníků třetích stran

**ID**: NIST SP 800-53 R4 PS-7 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1529 – zabezpečení zaměstnanců třetí strany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd74fdc92-1cb8-4a34-9978-8556425cd14c) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1529.json) |
|[Microsoft Managed Control 1530 – zabezpečení zaměstnanců třetí strany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e8f9566-29f1-49cd-b61f-f8628a3cf993) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1530.json) |
|[Microsoft Managed Control 1531 – zabezpečení zaměstnanců třetí strany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0643e0c-eee5-4113-8684-c608d05c5236) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1531.json) |
|[Microsoft Managed Control 1532 – zabezpečení zaměstnanců třetí strany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2c66299-9017-4d95-8040-8bdbf7901d52) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1532.json) |
|[Microsoft Managed Control 1533 – zabezpečení zaměstnanců třetí strany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbba2a036-fb3b-4261-b1be-a13dfb5fbcaa) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1533.json) |

### <a name="personnel-sanctions"></a>Sankce zaměstnanců

**ID**: NIST SP 800-53 R4 PS-8 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1534 – postihy zaměstnanců](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b2b263e-cd05-4488-bcbf-4debec7a17d9) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1534.json) |
|[Microsoft Managed Control 1535 – postihy zaměstnanců](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9a165d2-967d-4733-8399-1074270dae2e) |Společnost Microsoft implementuje tento ovládací prvek zabezpečení zaměstnanců |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1535.json) |

## <a name="risk-assessment"></a>Posouzení rizika

### <a name="risk-assessment-policy-and-procedures"></a>Zásady a postupy hodnocení rizik

**ID**: NIST SP 800-53 R4 RA-1 – **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1536 – zásady a postupy hodnocení rizik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e40d9de-2ad4-4cb5-8945-23143326a502) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1536.json) |
|[Microsoft Managed Control 1537 – zásady a postupy hodnocení rizik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb19454ca-0d70-42c0-acf5-ea1c1e5726d1) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1537.json) |

### <a name="security-categorization"></a>Kategorizace zabezpečení

**ID**: NIST SP 800-53 R4 RA-2 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1538 – kategorizace zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d7658b2-e827-49c3-a2ae-6d2bd0b45874) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1538.json) |
|[Microsoft Managed Control 1539 – kategorizace zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faabb155f-e7a5-4896-a767-e918bfae2ee0) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1539.json) |
|[Microsoft Managed Control 1540 – kategorizace zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff771f8cb-6642-45cc-9a15-8a41cd5c6977) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1540.json) |

### <a name="risk-assessment"></a>Posouzení rizika

**ID**: NIST SP 800-53 R4 Ra-3 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1541 – posouzení rizik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F70f6af82-7be6-44aa-9b15-8b9231b2e434) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1541.json) |
|[Microsoft Managed Control 1542 – posouzení rizik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feab340d0-3d55-4826-a0e5-feebfeb0131d) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1542.json) |
|[Microsoft Managed Control 1543 – posouzení rizik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd00b778-b5b5-49c0-a994-734ea7bd3624) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1543.json) |
|[Microsoft Managed Control 1544 – posouzení rizik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F43ced7c9-cd53-456b-b0da-2522649a4271) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1544.json) |
|[Microsoft Managed Control 1545 – posouzení rizik](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3f4b171a-a56b-4328-8112-32cf7f947ee1) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1545.json) |

### <a name="vulnerability-scanning"></a>Kontrola ohrožení zabezpečení

**ID**: NIST SP 800-53 R4 RA-5 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Auditujte všechny spravované instance SQL bez pokročilých zabezpečení dat. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Auditovat SQL servery bez pokročilých zabezpečení dat |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Microsoft Managed Control 1546 – kontrola ohrožení zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ce1ea7e-4038-4e53-82f4-63e8859333c1) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1546.json) |
|[Microsoft Managed Control 1547 – kontrola ohrožení zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58abf9b8-c6d4-4b4b-bfb9-fe98fe295f52) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1547.json) |
|[Microsoft Managed Control 1548 – kontrola ohrožení zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3afe6c78-6124-4d95-b85c-eb8c0c9539cb) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1548.json) |
|[Microsoft Managed Control 1549 – kontrola ohrožení zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd6976a08-d969-4df2-bb38-29556c2eb48a) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1549.json) |
|[Microsoft Managed Control 1550 – kontrola ohrožení zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F902908fb-25a8-4225-a3a5-5603c80066c9) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1550.json) |
|[Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Servery, které nesplňují nakonfigurované směrné plány, se budou monitorovat Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Auditujte ohrožení zabezpečení operačního systému ve službě Virtual Machine Scale Sets, abyste je chránili před útoky. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[Ohrožení zabezpečení vašich databází SQL by mělo být opraveno](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Sledujte výsledky kontroly ohrožení zabezpečení a doporučení pro napravení ohrožení zabezpečení databáze. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F760a85ff-6162-42b3-8d70-698e268f648c) |Monitoruje chyby zabezpečení zjištěné řešením posouzení ohrožení zabezpečení a virtuální počítače bez řešení posouzení ohrožení zabezpečení v Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning--update-tool-capability"></a>Kontrola ohrožení zabezpečení | Možnost aktualizovat Nástroj

**ID**: NIST SP 800-53 R4 RA-5 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1551 – \| možnost aktualizace nástroje pro kontrolu ohrožení zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bbda922-0172-4095-89e6-5b4a0bf03af7) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1551.json) |

### <a name="vulnerability-scanning--update-by-frequency--prior-to-new-scan--when-identified"></a>Kontrola ohrožení zabezpečení | Aktualizovat četností/před novou kontrolou/po identifikaci

**ID**: NIST SP 800-53 R4 RA-5 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1552 – Chyba při vyhledávání \| aktualizace frekvencí/před novou kontrolou/po identifikaci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F43684572-e4f1-4642-af35-6b933bc506da) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1552.json) |

### <a name="vulnerability-scanning--breadth--depth-of-coverage"></a>Kontrola ohrožení zabezpečení | Šířka/hloubka pokrytí

**ID**: NIST SP 800-53 R4 RA-5 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1553-Chyba kontroly \| šířky a hloubky pokrytí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e5225fe-cdfb-4fce-9aec-0fe20dd53b62) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1553.json) |

### <a name="vulnerability-scanning--discoverable-information"></a>Kontrola ohrožení zabezpečení | Zjistitelné informace

**ID**: NIST SP 800-53 R4 RA-5 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1554-ohrožení zabezpečení při kontrole \| zjistitelných informací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10984b4e-c93e-48d7-bf20-9c03b04e9eca) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1554.json) |

### <a name="vulnerability-scanning--privileged-access"></a>Kontrola ohrožení zabezpečení | Privilegovaný přístup

**ID**: NIST SP 800-53 R4 RA-5 (5) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1555 – Chyba při prohledávání \| privilegovaného přístupu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5afa8cab-1ed7-4e40-884c-64e0ac2059cc) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1555.json) |

### <a name="vulnerability-scanning--automated-trend-analyses"></a>Kontrola ohrožení zabezpečení | Automatizované analýzy trendů

**ID**: NIST SP 800-53 R4 RA-5 (6) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1556-ohrožení zabezpečení při kontrole \| automatizovaných analýz trendů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F391ff8b3-afed-405e-9f7d-ef2f8168d5da) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1556.json) |

### <a name="vulnerability-scanning--review-historic-audit-logs"></a>Kontrola ohrožení zabezpečení | Kontrola historických protokolů auditu

**ID**: NIST SP 800-53 R4 RA-5 (8) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1557-Chyba při \| kontrole historických protokolů auditu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36fbe499-f2f2-41b6-880e-52d7ea1d94a5) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1557.json) |

### <a name="vulnerability-scanning--correlate-scanning-information"></a>Kontrola ohrožení zabezpečení | Korelace informací o kontrole

**ID**: NIST SP 800-53 R4 RA-5 (10) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1558 – kontrola ohrožení zabezpečení \| koreluje informace o kontrole](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F65592b16-4367-42c5-a26e-d371be450e17) |Společnost Microsoft implementuje tento ovládací prvek posouzení rizika |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1558.json) |

## <a name="system-and-services-acquisition"></a>Získání systému a služeb

### <a name="system-and-services-acquisition-policy-and-procedures"></a>Zásady a postupy pořízení systému a služeb

**ID**: NIST SP 800-53 R4 SA-1 – **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1559 – zásady a postupy pořízení systému a služeb](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45692294-f074-42bd-ac54-16f1a3c07554) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1559.json) |
|[Microsoft Managed Control 1560 – zásady a postupy pořízení systému a služeb](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe29e0915-5c2f-4d09-8806-048b749ad763) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1560.json) |

### <a name="allocation-of-resources"></a>Přidělení prostředků

**ID**: NIST SP 800-53 R4 SA-2 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1561 – přidělení prostředků](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40364c3f-c331-4e29-b1e3-2fbe998ba2f5) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1561.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1562 – přidělení prostředků](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd4142013-7964-4163-a313-a900301c2cef) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1562.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1563 – přidělení prostředků](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9afe2edf-232c-4fdf-8e6a-e867a5c525fd) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1563.json) |

### <a name="system-development-life-cycle"></a>Životní cyklus vývoje systému

**ID**: NIST SP 800-53 R4 SA-3 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1564-životní cyklus vývoje systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F157f0ef9-143f-496d-b8f9-f8c8eeaad801) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1564.json) |
|[Microsoft Managed Control 1565-životní cyklus vývoje systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45ce2396-5c76-4654-9737-f8792ab3d26b) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1565.json) |
|[Microsoft Managed Control 1566-životní cyklus vývoje systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50ad3724-e2ac-4716-afcc-d8eabd97adb9) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1566.json) |
|[Microsoft Managed Control 1567-životní cyklus vývoje systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe72edbf6-aa61-436d-a227-0f32b77194b3) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1567.json) |

### <a name="acquisition-process"></a>Proces pořízení

**ID**: NIST SP 800-53 R4 SA-4 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1568 – proces získání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6a8eae8-9854-495a-ac82-d2cd3eac02a6) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1568.json) |
|[Spravovaný ovládací prvek Microsoft 1569 – proces získání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad2f8e61-a564-4dfd-8eaa-816f5be8cb34) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1569.json) |
|[Spravovaný ovládací prvek Microsoft 1570 – proces získání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7fcf38d-bb09-4600-be7d-825046eb162a) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1570.json) |
|[Spravovaný ovládací prvek Microsoft 1571 – proces získání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb11c985b-f2cd-4bd7-85f4-b52426edf905) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1571.json) |
|[Spravovaný ovládací prvek Microsoft 1572 – proces získání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04f5fb00-80bb-48a9-a75b-4cb4d4c97c36) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1572.json) |
|[Spravovaný ovládací prvek Microsoft 1573 – proces získání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58c93053-7b98-4cf0-b99f-1beb985416c2) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1573.json) |
|[Spravovaný ovládací prvek Microsoft 1574 – proces získání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f935dab-83d6-47b8-85ef-68b8584161b9) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1574.json) |

### <a name="acquisition-process--functional-properties-of-security-controls"></a>Proces pořízení | Funkční vlastnosti ovládacích prvků zabezpečení

**ID**: NIST SP 800-53 R4 SA-4 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1575- \| Vlastnosti procesu získání řízení zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93e1bb73-1b08-4dbe-9c62-8e2e92e7ec41) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1575.json) |

### <a name="acquisition-process--design--implementation-information-for-security-controls"></a>Proces pořízení | Informace o návrhu a implementaci ovládacích prvků zabezpečení

**ID**: NIST SP 800-53 R4 SA-4 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1576 – \| informace o návrhu/implementaci procesu získání pro ovládací prvky zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f18c885-ade3-48c5-80b1-8f9216019c18) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1576.json) |

### <a name="acquisition-process--continuous-monitoring-plan"></a>Proces pořízení | Plán nepřetržitého monitorování

**ID**: NIST SP 800-53 R4 SA-4 (8) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1577 – plán pro \| průběžné monitorování procesu získání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd922484a-8cfc-4a6b-95a4-77d6a685407f) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1577.json) |

### <a name="acquisition-process--functions--ports--protocols--services-in-use"></a>Proces pořízení | Funkce, porty/protokoly/služby, které se používají

**ID**: NIST SP 800-53 R4 SA-4 (9) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1578 – funkce procesu získání \| /porty/protokoly/služby, které se používají](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45b7b644-5f91-498e-9d89-7402532d3645) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1578.json) |

### <a name="acquisition-process--use-of-approved-piv-products"></a>Proces pořízení | Použití schválených produktů piv

**ID**: NIST SP 800-53 R4 SA-4 (10) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1579 – \| použití schválených produktů piv v procesu získání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e54c7ef-7457-430b-9a3e-ef8881d4a8e0) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1579.json) |

### <a name="information-system-documentation"></a>Dokumentace k informačnímu systému

**ID**: NIST SP 800-53 R4 SA-5 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1580 – dokumentace k informačnímu systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F854db8ac-6adf-42a0-bef3-b73f764f40b9) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1580.json) |
|[Microsoft Managed Control 1581 – dokumentace k informačnímu systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F742b549b-7a25-465f-b83c-ea1ffb4f4e0e) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1581.json) |
|[Microsoft Managed Control 1582 – dokumentace k informačnímu systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcd9e2f38-259b-462c-bfad-0ad7ab4e65c5) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1582.json) |
|[Microsoft Managed Control 1583 – dokumentace k informačnímu systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0882d488-8e80-4466-bc0f-0cd15b6cb66d) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1583.json) |
|[Microsoft Managed Control 1584 – dokumentace k informačnímu systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5864522b-ff1d-4979-a9f8-58bee1fb174c) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1584.json) |

### <a name="security-engineering-principles"></a>Zásady zabezpečení

**ID**: NIST SP 800-53 R4 SA-8 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1585 – zásady zabezpečení technického řízení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd57f8732-5cdc-4cda-8d27-ab148e1f3a55) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1585.json) |

### <a name="external-information-system-services"></a>Služby externích informačních systémů

**ID**: NIST SP 800-53 R4 SA-9 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1586 – externí informační systémové služby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e3b2fbd-8f37-4766-a64d-3f37703dcb51) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1586.json) |
|[Microsoft Managed Control 1587 – externí informační systémové služby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32820956-9c6d-4376-934c-05cd8525be7c) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1587.json) |
|[Microsoft Managed Control 1588 – externí informační systémové služby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68ebae26-e0e0-4ecb-8379-aabf633b51e9) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1588.json) |

### <a name="external-information-system-services--risk-assessments--organizational-approvals"></a>Služby externích informačních systémů | Posouzení rizik/schválení organizace

**ID**: NIST SP 800-53 R4 SA-9 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1589-externí informace o \| rizicích pro posouzení rizik a organizace schválení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86ec7f9b-9478-40ff-8cfd-6a0d510081a8) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1589.json) |
|[Microsoft Managed Control 1590-externí informace o \| rizicích pro posouzení rizik a organizace schválení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf296b8c-f391-4ea4-9198-be3c9d39dd1f) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1590.json) |

### <a name="external-information-system-services--identification-of-functions--ports--protocols--services"></a>Služby externích informačních systémů | Identifikace funkcí/portů/protokolů/služeb

**ID**: NIST SP 800-53 R4 SA-9 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1591 – externí informační systém \| Ident služby. Funkce/porty/protokoly/služby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff751cdb7-fbee-406b-969b-815d367cb9b3) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1591.json) |

### <a name="external-information-system-services--consistent-interests-of-consumers-and-providers"></a>Služby externích informačních systémů | Konzistentní zájmy spotřebitelů a poskytovatelů

**ID**: NIST SP 800-53 R4 SA-9 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1592 – externí informace o \| konzistentních zájmech uživatelů a poskytovatelů služeb](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d01ba6c-289f-42fd-a408-494b355b6222) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1592.json) |

### <a name="external-information-system-services--processing-storage-and-service-location"></a>Služby externích informačních systémů | Zpracování, úložiště a umístění služby

**ID**: NIST SP 800-53 R4 SA-9 (5) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1593 – \| zpracování, úložiště a umístění služby externími informačními systémy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2cd0a426-b5f5-4fe0-9539-a6043cdbc6fa) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1593.json) |

### <a name="developer-configuration-management"></a>Správa konfigurace pro vývojáře

**ID**: NIST SP 800-53 R4 SA-10 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1594 – Správa konfigurace pro vývojáře](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F042ba2a1-8bb8-45f4-b080-c78cf62b90e9) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1594.json) |
|[Microsoft Managed Control 1595 – Správa konfigurace pro vývojáře](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1e0414e7-6ef5-4182-8076-aa82fbb53341) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1595.json) |
|[Microsoft Managed Control 1596 – Správa konfigurace pro vývojáře](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21e25e01-0ae0-41be-919e-04ce92b8e8b8) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1596.json) |
|[Microsoft Managed Control 1597 – Správa konfigurace pro vývojáře](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68b250ec-2e4f-4eee-898a-117a9fda7016) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1597.json) |
|[Microsoft Managed Control 1598 – Správa konfigurace pro vývojáře](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae7e1f5e-2d63-4b38-91ef-bce14151cce3) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1598.json) |

### <a name="developer-configuration-management--software--firmware-integrity-verification"></a>Správa konfigurace pro vývojáře | Ověření integrity softwaru/firmwaru

**ID**: NIST SP 800-53 R4 SA-10 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1599 – \| ověření integrity softwaru/firmwaru správy konfigurace pro vývojáře](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0004bbf0-5099-4179-869e-e9ffe5fb0945) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1599.json) |

### <a name="developer-security-testing-and-evaluation"></a>Testování a vyhodnocení zabezpečení pro vývojáře

**ID**: NIST SP 800-53 R4 SA-11 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1600 – testování a vyhodnocení zabezpečení pro vývojáře](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc53f3123-d233-44a7-930b-f40d3bfeb7d6) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1600.json) |
|[Microsoft Managed Control 1601 – testování a vyhodnocení zabezpečení pro vývojáře](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ee79a0c-addf-4ce9-9b3c-d9576ed5e20e) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1601.json) |
|[Microsoft Managed Control 1602 – testování a vyhodnocení zabezpečení pro vývojáře](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fddae2e97-a449-499f-a1c8-aea4a7e52ec9) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1602.json) |
|[Microsoft Managed Control 1603 – testování a vyhodnocení zabezpečení pro vývojáře](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b909c26-162f-47ce-8e15-0c1f55632eac) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1603.json) |
|[Microsoft Managed Control 1604 – testování a vyhodnocení zabezpečení pro vývojáře](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44dbba23-0b61-478e-89c7-b3084667782f) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1604.json) |

### <a name="developer-security-testing-and-evaluation--static-code-analysis"></a>Testování a vyhodnocení zabezpečení pro vývojáře | Analýza statického kódu

**ID**: NIST SP 800-53 R4 SA-11 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1605 – \| Analýza statického kódu a testování zabezpečení pro vývojáře](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0062eb8b-dc75-4718-8ea5-9bb4a9606655) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1605.json) |

### <a name="developer-security-testing-and-evaluation--threat-and-vulnerability-analyses"></a>Testování a vyhodnocení zabezpečení pro vývojáře | Analýzy hrozeb a ohrožení zabezpečení

**ID**: NIST SP 800-53 R4 SA-11 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1606 – analýzy zabezpečení a bezpečnostní hrozby pro vývoj a testy \| ohrožení zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbaa8a9a4-5bbe-4c72-98f6-a3a47ae2b1ca) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1606.json) |

### <a name="developer-security-testing-and-evaluation--dynamic-code-analysis"></a>Testování a vyhodnocení zabezpečení pro vývojáře | Analýza dynamického kódu

**ID**: NIST SP 800-53 R4 SA-11 (8) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1607 – testování zabezpečení pro vývojáře a \| Analýza dynamického kódu pro vyhodnocení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F976a74cf-b192-4d35-8cab-2068f272addb) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1607.json) |

### <a name="supply-chain-protection"></a>Ochrana dodavatelských řetězců

**ID**: NIST SP 800-53 R4 SA-12 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1608 – ochrana dodavatelských řetězců](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb73b7b3b-677c-4a2a-b949-ad4dc4acd89f) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1608.json) |

### <a name="development-process-standards-and-tools"></a>Vývojové procesy, standardy a nástroje

**ID**: NIST SP 800-53 R4 SA-15 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1609 – proces vývoje, standardy a nástroje](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e93fa71-42ac-41a7-b177-efbfdc53c69f) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1609.json) |
|[Microsoft Managed Control 1610 – proces vývoje, standardy a nástroje](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9f3fb54-4222-46a1-a308-4874061f8491) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1610.json) |

### <a name="developer-provided-training"></a>Školení poskytované vývojářem

**ID**: NIST SP 800-53 R4 SA-16 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1611 – školení poskytované vývojářem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdda8a0c-ac32-43f6-b2f4-7dc1df03f43f) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1611.json) |

### <a name="developer-security-architecture-and-design"></a>Architektura a návrh zabezpečení pro vývojáře

**ID**: NIST SP 800-53 R4 SA-17 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1612 – architektura a návrh zabezpečení pro vývojáře](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2037b3d-8b04-4171-8610-e6d4f1d08db5) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1612.json) |
|[Microsoft Managed Control 1613 – architektura a návrh zabezpečení pro vývojáře](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe2ad78b-8748-4bff-a924-f74dfca93f30) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1613.json) |
|[Microsoft Managed Control 1614 – architektura a návrh zabezpečení pro vývojáře](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8154e3b3-cc52-40be-9407-7756581d71f6) |Společnost Microsoft implementuje tento ovládací prvek pro pořízení systému a služeb |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1614.json) |

## <a name="system-and-communications-protection"></a>Ochrana systémů a komunikací

### <a name="system-and-communications-protection-policy-and-procedures"></a>Zásady a postupy ochrany systému a komunikace

**ID**: NIST SP 800-53 R4 SC-1 – **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1615 – zásady a postupy ochrany pro systém a komunikaci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff35e02aa-0a55-49f8-8811-8abfa7e6f2c0) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1615.json) |
|[Microsoft Managed Control 1616 – zásady a postupy ochrany pro systém a komunikaci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2006457a-48b3-4f7b-8d2e-1532287f9929) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1616.json) |

### <a name="application-partitioning"></a>Dělení aplikací

**ID**: NIST SP 800-53 R4 SC-2 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1617-dělení aplikací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa631d8f5-eb81-4f9d-9ee1-74431371e4a3) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1617.json) |

### <a name="security-function-isolation"></a>Izolace funkcí zabezpečení

**ID**: NIST SP 800-53 R4 SC-3 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1618 – izolace funkcí zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff52f89aa-4489-4ec4-950e-8c96a036baa9) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1618.json) |

### <a name="information-in-shared-resources"></a>Informace ve sdílených prostředcích

**ID**: NIST SP 800-53 R4 SC-4 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1619 – informace ve sdílených prostředcích](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc722e569-cb52-45f3-a643-836547d016e1) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1619.json) |

### <a name="denial-of-service-protection"></a>Odmítnutí služby Service Protection

**ID**: NIST SP 800-53 R4 SC-5 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Měla by být povolená Azure DDoS Protection Standard.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |DDoS Protection Standard by měla být povolená pro všechny virtuální sítě s podsítí, která je součástí aplikační brány s veřejnou IP adresou. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |
|[Microsoft Managed Control 1620 – odmítnutí služby Service Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd17c826b-1dec-43e1-a984-7b71c446649c) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1620.json) |

### <a name="resource-availability"></a>Dostupnost prostředků

**ID**: NIST SP 800-53 R4 SC-6 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1621 – dostupnost prostředků](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cb9f731-744a-4691-a481-ca77b0411538) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1621.json) |

### <a name="boundary-protection"></a>Ochrana hranic

**ID**: NIST SP 800-53 R4 SC-7 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Přístup přes internetový koncový bod by měl být omezený.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center identifikoval některé z příchozích pravidel vaší skupiny zabezpečení sítě tak, aby byly příliš opravňující. Příchozí pravidla by neměla umožňovat přístup z rozsahů "any" nebo "Internet". To může potenciálně umožnit útočníkům snadno cílit na vaše prostředky. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[Doporučení adaptivního posílení zabezpečení sítě by se měla použít na internetových virtuálních počítačích.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analyzuje modely přenosů internetových virtuálních počítačů a poskytuje doporučení pro pravidla skupiny zabezpečení sítě, která omezují potenciální útok na plochu. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Microsoft Managed Control 1622 – hranice ochrany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fecf56554-164d-499a-8d00-206b07c27bed) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1622.json) |
|[Microsoft Managed Control 1623 – hranice ochrany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02ce1b22-412a-4528-8630-c42146f917ed) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1623.json) |
|[Microsoft Managed Control 1624 – hranice ochrany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F37d079e3-d6aa-4263-a069-dd7ac6dd9684) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1624.json) |
|[Účty úložiště by měly omezovat síťový přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Přístup k síti účtů úložiště by měl být omezený. Nakonfigurujte Síťová pravidla, aby k účtu úložiště měly přístup jenom aplikace z povolených sítí. Aby bylo možné v připojeních z konkrétních internetových nebo místních klientů přistupovat k provozu z konkrétních virtuálních sítí Azure nebo do rozsahů veřejných IP adres sítě. |Audit, zamítnutí, zakázáno |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="boundary-protection--access-points"></a>Ochrana hranic | Přístupové body

**ID**: NIST SP 800-53 R4 SC-7 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Porty pro správu virtuálních počítačů by měly být chráněné pomocí řízení přístupu k síti za běhu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Přístup k síti je možný jenom v čase (JIT) se monitoruje Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Microsoft Managed Control 1625 – \| přístupové body ochrany hranic](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9b66a4d-70a1-4b47-8fa1-289cec68c605) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1625.json) |

### <a name="boundary-protection--external-telecommunications-services"></a>Ochrana hranic | Externí telekomunikační služby

**ID**: NIST SP 800-53 R4 SC-7 (4) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Porty pro správu virtuálních počítačů by měly být chráněné pomocí řízení přístupu k síti za běhu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Přístup k síti je možný jenom v čase (JIT) se monitoruje Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Microsoft Managed Control 1626 – ochrana hranic \| externí telekomunikační služby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8f6bddd-6d67-439a-88d4-c5fe39a79341) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1626.json) |
|[Microsoft Managed Control 1627 – ochrana hranic \| externí telekomunikační služby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd73310d-76fc-422d-bda4-3a077149f179) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1627.json) |
|[Microsoft Managed Control 1628 – ochrana hranic \| externí telekomunikační služby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F67de62b4-a737-4781-8861-3baed3c35069) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1628.json) |
|[Microsoft Managed Control 1629 – ochrana hranic \| externí telekomunikační služby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc171b095-7756-41de-8644-a062a96043f2) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1629.json) |
|[Microsoft Managed Control 1630 – ochrana hranic \| externí telekomunikační služby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3643717a-3897-4bfd-8530-c7c96b26b2a0) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1630.json) |

### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>Ochrana hranic | Odepřít ve výchozím nastavení/povolit výjimku

**ID**: NIST SP 800-53 R4 SC-7 (5) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1631 – omezení ochrany hranic \| ve výchozím nastavení/Povolit při výjimce](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74ae9b8e-e7bb-4c9c-992f-c535282f7a2c) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1631.json) |

### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>Ochrana hranic | Zabránit rozdělenému tunelování pro vzdálená zařízení

**ID**: NIST SP 800-53 R4 SC-7 (7) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1632 – hranice ochrany \| brání rozdělenému tunelování pro vzdálená zařízení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ce9073a-77fa-48f0-96b1-87aa8e6091c2) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1632.json) |

### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>Ochrana hranic | Směrování provozu na ověřené proxy servery

**ID**: NIST SP 800-53 R4 SC-7 (8) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1633 – přenos hraniční ochrany \| na ověřené proxy servery](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F07557aa0-e02f-4460-9a81-8ecd2fed601a) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1633.json) |

### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>Ochrana hranic | Zabránit neoprávněnému exfiltrace

**ID**: NIST SP 800-53 R4 SC-7 (10) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1634 – hranice ochrany \| brání neoprávněnému exfiltrace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F292a7c44-37fa-4c68-af7c-9d836955ded2) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1634.json) |

### <a name="boundary-protection--host-based-protection"></a>Ochrana hranic | Ochrana založená na hostiteli

**ID**: NIST SP 800-53 R4 SC-7 (12) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1635 – \| ochrana na základě hostitele ochrany hranic](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87551b5d-1deb-4d0f-86cc-9dc14cb4bf7e) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1635.json) |

### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>Ochrana hranic | Izolace nástrojů zabezpečení/mechanismů/součástí podpory

**ID**: NIST SP 800-53 R4 SC-7 (13) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1636 – izolace ochrany hranic \| v nástrojích zabezpečení/mechanismech/součástech podpory](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7b694eed-7081-43c6-867c-41c76c961043) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1636.json) |

### <a name="boundary-protection--fail-secure"></a>Ochrana hranic | Neúspěšné zabezpečení

**ID**: NIST SP 800-53 R4 SC-7 (18) **vlastnictví**: Microsoft

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1637 – hranice ochrany se \| nedaří zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4075bedc-c62a-4635-bede-a01be89807f3) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1637.json) |

### <a name="boundary-protection--dynamic-isolation--segregation"></a>Ochrana hranic | Dynamická izolace/oddělení

**ID**: NIST SP 800-53 R4 SC-7 (20) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1638 – \| Dynamická izolace/oddělení ochrany hranic](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F49b99653-32cd-405d-a135-e7d60a9aae1f) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1638.json) |

### <a name="boundary-protection--isolation-of-information-system-components"></a>Ochrana hranic | Izolace součástí informačního systému

**ID**: NIST SP 800-53 R4 SC-7 (21) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1639 – izolace ochrany hranic součástí \| informačních systémů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F78e8e649-50f6-4fe3-99ac-fedc2e63b03f) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1639.json) |

### <a name="transmission-confidentiality-and-integrity"></a>Důvěrnost a integrita přenosu

**ID**: NIST SP 800-53 R4 SC-8 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1640 – důvěrnost a integrita přenosu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05a289ce-6a20-4b75-a0f3-dc8601b6acd0) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1640.json) |

### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Důvěrnost a integrita přenosu | Kryptografická nebo alternativní fyzická ochrana

**ID**: NIST SP 800-53 R4 SC-8 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Aplikace API by měla být přístupná jen přes protokol HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Auditovat webové servery systému Windows, které nepoužívají protokoly zabezpečených komunikací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5752e6d6-1206-46d8-8ab1-ecc2f71a8112) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud klíč registru HKLM: \ SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols zahrnuje protokoly méně bezpečné než vybrané v parametru zásad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecureWebProtocol_AINE.json) |
|[Function App by měl být přístupný jenom přes HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Microsoft Managed Control 1641 – důvěrnost přenosů a \| kryptografická integrita nebo alternativní fyzická ochrana](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd39d4f68-7346-4133-8841-15318a714a24) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1641.json) |
|[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditovat povolování jenom připojení přes SSL do Azure cache pro Redis. Použití zabezpečených připojení zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako jsou například prostředníky, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Měl by se povolit zabezpečený přenos do účtů úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Audit požadavek na zabezpečený přenos v účtu úložiště. Zabezpečený přenos je možnost, která vynutí, aby váš účet úložiště přijímal požadavky jenom od zabezpečených připojení (HTTPS). Použití protokolu HTTPS zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako je třeba prostředník, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Webová aplikace by měla být přístupná jen přes protokol HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="network-disconnect"></a>Síťové odpojení

**ID**: NIST SP 800-53 R4 SC-10 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1642 – odpojení sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53397227-5ee3-4b23-9e5e-c8a767ce6928) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1642.json) |

### <a name="cryptographic-key-establishment-and-management"></a>Vytváření a Správa kryptografického klíče

**ID**: NIST SP 800-53 R4 SC-12 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1643 – vytváření a Správa kryptografického klíče](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d8d492c-dd7a-46f7-a723-fa66a425b87c) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1643.json) |

### <a name="cryptographic-key-establishment-and-management--availability"></a>Vytváření a Správa kryptografického klíče | Dostupnosti

**ID**: NIST SP 800-53 R4 SC-12 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1644 – dostupnost a Správa kryptografického klíče \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7211477-c970-446b-b4af-062f37461147) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1644.json) |

### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>Vytváření a Správa kryptografického klíče | Symetrické klíče

**ID**: NIST SP 800-53 R4 SC-12 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1645 – symetrické klíče pro vytváření a správu kryptografických klíčů \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafbd0baf-ff1a-4447-a86f-088a97347c0c) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1645.json) |

### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>Vytváření a Správa kryptografického klíče | Asymetrické klíče

**ID**: NIST SP 800-53 R4 SC-12 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1646 – vytváření kryptografických klíčů a Správa \| asymetrických klíčů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F506814fa-b930-4b10-894e-a45b98c40e1a) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1646.json) |

### <a name="cryptographic-protection"></a>Kryptografická ochrana

**ID**: NIST SP 800-53 R4 SC-13 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1647 – kryptografická ochrana](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F791cfc15-6974-42a0-9f4c-2d4b82f4a78c) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1647.json) |

### <a name="collaborative-computing-devices"></a>Zařízení pro spolupráci s výpočetním prostředím

**ID**: NIST SP 800-53 R4 SC-15 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1648 – výpočetní zařízení pro spolupráci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a9eb14b-495a-4ebb-933c-ce4ef5264e32) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1648.json) |
|[Microsoft Managed Control 1649 – výpočetní zařízení pro spolupráci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26d292cc-b0b8-4c29-9337-68abc758bf7b) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1649.json) |

### <a name="public-key-infrastructure-certificates"></a>Certifikáty infrastruktury veřejných klíčů

**ID**: NIST SP 800-53 R4 SC-17 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1650 – certifikáty infrastruktury veřejných klíčů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F201d3740-bd16-4baf-b4b8-7cda352228b7) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1650.json) |

### <a name="mobile-code"></a>Mobilní kód

**ID**: NIST SP 800-53 R4 SC-18 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1651 – mobilní kód](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6db63528-c9ba-491c-8a80-83e1e6977a50) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1651.json) |
|[Microsoft Managed Control 1652 – mobilní kód](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6998e84a-2d29-4e10-8962-76754d4f772d) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1652.json) |
|[Microsoft Managed Control 1653 – mobilní kód](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1c00a7-7fd0-42b0-8c5b-c45f6fa1f71b) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1653.json) |

### <a name="voice-over-internet-protocol"></a>Voice over Internet Protocol

**ID**: NIST SP 800-53 R4 SC-19 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1654 – Voice over Internet Protocol](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a2ee16e-ab1f-414a-800b-d1608835862b) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1654.json) |
|[Microsoft Managed Control 1655 – Voice over Internet Protocol](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F121eab72-390e-4629-a7e2-6d6184f57c6b) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1655.json) |

### <a name="secure-name--address-resolution-service-authoritative-source"></a>Zabezpečený název/služba překladu adres (autoritativní zdroj)

**ID**: NIST SP 800-53 R4 SC-20 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1656 – služba překladu adres IP (autoritativní zdroj)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1cb067d5-c8b5-4113-a7ee-0a493633924b) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1656.json) |
|[Microsoft Managed Control 1657 – služba překladu adres IP (autoritativní zdroj)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90f01329-a100-43c2-af31-098996135d2b) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1657.json) |

### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>Zabezpečené jméno/služba překladu adres (rekurzivní nebo mezipaměti překladače)

**ID**: NIST SP 800-53 R4 SC-21 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1658 – služba překladu adres (rekurzivní nebo mezipaměti)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F063b540e-4bdc-4e7a-a569-3a42ddf22098) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1658.json) |

### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>Architektura a zřizování pro službu překladu názvů a adres

**ID**: NIST SP 800-53 R4 SC-22 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1659 – architektura a zřizování pro službu překladu názvů a adres](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35a4102f-a778-4a2e-98c2-971056288df8) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1659.json) |

### <a name="session-authenticity"></a>Pravost relace

**ID**: NIST SP 800-53 R4 SC-23 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1660 – pravost relace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F63096613-ce83-43e5-96f4-e588e8813554) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1660.json) |

### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>Pravost relace | Zrušit platnost identifikátorů relací při odhlášení

**ID**: NIST SP 800-53 R4 SC-23 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1661- \| při odhlášení neověřuje identifikátory relací.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c643c9a-1be7-4016-a5e7-e4bada052920) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1661.json) |

### <a name="fail-in-known-state"></a>Selhání ve známém stavu

**ID**: NIST SP 800-53 R4 SC-24 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1662 – chyba ve známém stavu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F165cb91f-7ea8-4ab7-beaf-8636b98c9d15) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1662.json) |

### <a name="protection-of-information-at-rest"></a>Ochrana informací v klidovém umístění

**ID**: NIST SP 800-53 R4 SC-28 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1663 – ochrana informací v klidovém znění](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60171210-6dde-40af-a144-bf2670518bfa) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1663.json) |

### <a name="protection-of-information-at-rest--cryptographic-protection"></a>Ochrana informací v klidovém znění | Kryptografická ochrana

**ID**: NIST SP 800-53 R4 SC-28 (1) **vlastnictví**: zákazník

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Auditujte všechny spravované instance SQL bez pokročilých zabezpečení dat. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Auditovat SQL servery bez pokročilých zabezpečení dat |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Na virtuálních počítačích by se mělo použít šifrování disku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Virtuální počítače bez povoleného šifrování disku se monitorují Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |
|[Microsoft Managed Control 1664 – ochrana informací v REST \| kryptografické ochraně](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2cdf6b8-9505-4619-b579-309ba72037ac) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1664.json) |
|[Je třeba povolit transparentní šifrování dat databází SQL.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Transparentní šifrování dat by mělo být povoleno pro ochranu neaktivních dat a splnění požadavků na dodržování předpisů. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="process-isolation"></a>Izolace procesů

**ID**: NIST SP 800-53 R4 SC-39 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1665 – izolace procesů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5df3a55c-8456-44d4-941e-175f79332512) |Společnost Microsoft implementuje tento systém a řízení ochrany komunikace |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1665.json) |

## <a name="system-and-information-integrity"></a>Integrita systémů a informací

### <a name="system-and-information-integrity-policy-and-procedures"></a>Zásady a postupy integrity systému a informací

**ID**: NIST SP 800-53 R4-1 – **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1666 – zásady a postupy integrity systémů a informací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12e30ee3-61e6-4509-8302-a871e8ebb91e) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1666.json) |
|[Microsoft Managed Control 1667 – zásady a postupy integrity systémů a informací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd61880dc-6e38-4f2a-a30c-3406a98f8220) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1667.json) |

### <a name="flaw-remediation"></a>Náprava chyby

**ID**: NIST SP 800-53 R4 si-2 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravované řízení společnosti Microsoft 1668 – Oprava chyby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fb0966e-be1d-42c3-baca-60df5c0bcc61) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1668.json) |
|[Spravované řízení společnosti Microsoft 1669 – Oprava chyby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48f2f62b-5743-4415-a143-288adc0e078d) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1669.json) |
|[Spravované řízení společnosti Microsoft 1670 – Oprava chyby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc6108469-57ee-4666-af7e-79ba61c7ae0c) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1670.json) |
|[Spravované řízení společnosti Microsoft 1671 – Oprava chyby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c5bbef7-a316-415b-9b38-29753ce8e698) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1671.json) |
|[Musí být nainstalované aktualizace systému ve virtuálních počítačích Virtual Machine Scale Sets.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Auditujte, jestli existují chybějící aktualizace zabezpečení systému a důležité aktualizace, které byste měli nainstalovat, abyste měli jistotu, že jsou vaše virtuální počítače s Windows a Linux zabezpečené. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |
|[Na počítače by se měly nainstalovat aktualizace systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Chybějící aktualizace systému zabezpečení na vašich serverech se monitorují Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |
|[Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Servery, které nesplňují nakonfigurované směrné plány, se budou monitorovat Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Auditujte ohrožení zabezpečení operačního systému ve službě Virtual Machine Scale Sets, abyste je chránili před útoky. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[Ohrožení zabezpečení vašich databází SQL by mělo být opraveno](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Sledujte výsledky kontroly ohrožení zabezpečení a doporučení pro napravení ohrožení zabezpečení databáze. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F760a85ff-6162-42b3-8d70-698e268f648c) |Monitoruje chyby zabezpečení zjištěné řešením posouzení ohrožení zabezpečení a virtuální počítače bez řešení posouzení ohrožení zabezpečení v Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |

### <a name="flaw-remediation--central-management"></a>Oprava chyby | Centrální správa

**ID**: NIST SP 800-53 R4 si-2 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1672-Chyba při nápravě chyby v \| centrální správě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb45fe972-904e-45a4-ac20-673ba027a301) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1672.json) |

### <a name="flaw-remediation--automated-flaw-remediation-status"></a>Oprava chyby | Stav opravy automatizované chyby

**ID**: NIST SP 800-53 R4 si-2 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1673 – \| stav opravy chyby při automatické opravě chyby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdff0b90d-5a6f-491c-b2f8-b90aa402d844) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1673.json) |

### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Oprava chyby | Čas k nápravě chyb/srovnávacích testů pro nápravné akce

**ID**: NIST SP 800-53 R4 si-2 (3) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1674 – doba nápravy chyby pro nápravu \| vad/srovnávacích akcí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93e9e233-dd0a-4bde-aea5-1371bce0e002) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1674.json) |
|[Microsoft Managed Control 1675 – doba nápravy chyby pro nápravu \| vad/srovnávacích akcí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffacb66e0-1c48-478a-bed5-747a312323e1) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1675.json) |

### <a name="malicious-code-protection"></a>Ochrana škodlivých kódů

**ID**: NIST SP 800-53 R4 si-3 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Řešení ochrany koncových bodů by se mělo nainstalovat na Virtual Machine Scale Sets.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Auditujte existenci a stav řešení ochrany koncových bodů na virtuálních počítačích, které jsou v sadě škálování, a chránit je před hrozbami a ohroženími zabezpečení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1676 – ochrana škodlivých kódů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc10fb58b-56a8-489e-9ce3-7ffe24e78e4b) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1676.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1677 – ochrana škodlivých kódů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a248e1e-040f-43e5-bff2-afc3a57a3923) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1677.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1678 – ochrana škodlivých kódů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd533cb0-b416-4be7-8e86-4d154824dfd7) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1678.json) |
|[Spravovaný ovládací prvek společnosti Microsoft 1679 – ochrana škodlivých kódů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2cf42a28-193e-41c5-98df-7688e7ef0a88) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1679.json) |
|[Monitorovat chybějící Endpoint Protection v Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servery bez nainstalovaného agenta Endpoint Protection se budou monitorovat Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="malicious-code-protection--central-management"></a>Ochrana před škodlivým kódem | Centrální správa

**ID**: NIST SP 800-53 R4 si-3 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Řešení ochrany koncových bodů by se mělo nainstalovat na Virtual Machine Scale Sets.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Auditujte existenci a stav řešení ochrany koncových bodů na virtuálních počítačích, které jsou v sadě škálování, a chránit je před hrozbami a ohroženími zabezpečení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft Managed Control 1680 – centrální Správa ochrany před škodlivým kódem \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399cd6ee-0e18-41db-9dea-cde3bd712f38) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1680.json) |
|[Monitorovat chybějící Endpoint Protection v Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servery bez nainstalovaného agenta Endpoint Protection se budou monitorovat Azure Security Center jako doporučení. |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="malicious-code-protection--automatic-updates"></a>Ochrana před škodlivým kódem | Automatické aktualizace

**ID**: NIST SP 800-53 R4 si-3 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1681 – automatické aktualizace ochrany škodlivých kódů \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12623e7e-4736-4b2e-b776-c1600f35f93a) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1681.json) |

### <a name="malicious-code-protection--nonsignature-based-detection"></a>Ochrana před škodlivým kódem | Detekce na základě podpisu

**ID**: NIST SP 800-53 R4 si-3 (7) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1682 – detekce nebezpečného kódu – \| nezaložené na podpisu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F62b638c5-29d7-404b-8d93-f21e4b1ce198) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1682.json) |

### <a name="information-system-monitoring"></a>Sledování informací o systému

**ID**: NIST SP 800-53 R4 si-4 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[\[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Hlásí virtuální počítače jako nevyhovující, pokud image virtuálního počítače (OS) není v seznamu definovaném a Agent není nainstalovaný. Seznam imagí operačního systému se v průběhu času aktualizuje, protože se podpora aktualizuje. |auditIfNotExists |[1.0.0 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[V případě spravované instance SQL by mělo být povolené rozšířené zabezpečení dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Auditujte všechny spravované instance SQL bez pokročilých zabezpečení dat. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Auditovat SQL servery bez pokročilých zabezpečení dat |AuditIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Audit Log Analyticsho nasazení agenta ve Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Nahlásí sadu škálování virtuálního počítače jako nedodržující předpisy, pokud se image virtuálního počítače (OS) nenachází v seznamu definovaném a Agent není nainstalovaný. Seznam imagí operačního systému se v průběhu času aktualizuje, protože se podpora aktualizuje. |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Audit Log Analytics pracovní prostor pro virtuální počítač – neshoda sestavy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Hlásí virtuální počítače jako nedodržující předpisy, pokud se neprotokolují do Log Analyticsho pracovního prostoru zadaného v přiřazení zásad nebo iniciativ. |ověřen |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[Microsoft Managed Control 1683 – informační systém sledování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c79fee4-88dd-44ce-bbd4-4de88948c4f8) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1683.json) |
|[Microsoft Managed Control 1684 – informační systém sledování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16bfdb59-db38-47a5-88a9-2e9371a638cf) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1684.json) |
|[Microsoft Managed Control 1685 – informační systém sledování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36b0ef30-366f-4b1b-8652-a3511df11f53) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1685.json) |
|[Microsoft Managed Control 1686 – informační systém sledování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe17085c5-0be8-4423-b39b-a52d3d1402e5) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1686.json) |
|[Microsoft Managed Control 1687 – informační systém sledování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a87fc7f-301e-49f3-ba2a-4d74f424fa97) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1687.json) |
|[Microsoft Managed Control 1688 – informační systém sledování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F063c3f09-e0f0-4587-8fd5-f4276fae675f) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1688.json) |
|[Microsoft Managed Control 1689 – informační systém sledování](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fde901f2f-a01a-4456-97f0-33cda7966172) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1689.json) |

### <a name="information-system-monitoring--system-wide-intrusion-detection-system"></a>Sledování informací o systému | Systém zjišťování neoprávněných vniknutí do systému

**ID**: NIST SP 800-53 R4 si-4 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1690 – \| systém pro zjišťování neoprávněných vniknutí do systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2567a23-d1c3-4783-99f3-d471302a4d6b) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1690.json) |

### <a name="information-system-monitoring--automated-tools-for-real-time-analysis"></a>Sledování informací o systému | Automatizované nástroje pro analýzu v reálném čase

**ID**: NIST SP 800-53 R4 si-4 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1691 – informace o \| automatizovaném nástroji Sledování systému pro analýzu v reálném čase](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71475fb4-49bd-450b-a1a5-f63894c24725) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1691.json) |

### <a name="information-system-monitoring--inbound-and-outbound-communications-traffic"></a>Sledování informací o systému | Příchozí a odchozí komunikační provoz

**ID**: NIST SP 800-53 R4 si-4 (4) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1692 – informační systém sledování \| příchozího a odchozího komunikačního provozu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ecda928-9df4-4dd7-8f44-641a91e470e8) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1692.json) |

### <a name="information-system-monitoring--system-generated-alerts"></a>Sledování informací o systému | Výstrahy generované systémem

**ID**: NIST SP 800-53 R4 si-4 (5) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1693 – \| výstrahy vygenerované systémem pro monitorování systémových informací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa450eba6-2efc-4a00-846a-5804a93c6b77) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1693.json) |

### <a name="information-system-monitoring--analyze-communications-traffic-anomalies"></a>Sledování informací o systému | Analýza anomálií komunikačních přenosů

**ID**: NIST SP 800-53 R4 si-4 (11) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1694 – informace o sledování \| anomálií komunikace při analýze provozu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F426c4ac9-ff17-49d0-acd7-a13c157081c0) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1694.json) |

### <a name="information-system-monitoring--wireless-intrusion-detection"></a>Sledování informací o systému | Zjišťování vniknutí do bezdrátové sítě

**ID**: NIST SP 800-53 R4 si-4 (14) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1695 – informace o monitorování \| neoprávněného vniknutí bezdrátového systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13fcf812-ec82-4eda-9b89-498de9efd620) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1695.json) |

### <a name="information-system-monitoring--correlate-monitoring-information"></a>Sledování informací o systému | Korelace monitorovacích informací

**ID**: NIST SP 800-53 R4 si-4 (16) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek společnosti Microsoft 1696 – informace o \| sledování korelace sledování systému](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69d2a238-20ab-4206-a6dc-f302bf88b1b8) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1696.json) |

### <a name="information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Sledování informací o systému | Analýza provozu/tajnosti exfiltrace

**ID**: NIST SP 800-53 R4 si-4 (18) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1697 – informace o monitorování systémových systémů pro \| analýzu provozu/tajnosti exfiltrace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9873db2-18ad-46b3-a11a-1a1f8cbf0335) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1697.json) |

### <a name="information-system-monitoring--individuals-posing-greater-risk"></a>Sledování informací o systému | Jednotlivci, kteří přikročí větší riziko

**ID**: NIST SP 800-53 R4 si-4 (19) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1698 – informační systém sledování, kteří připravují \| větší riziko](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F31b752c1-05a9-432a-8fce-c39b56550119) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1698.json) |

### <a name="information-system-monitoring--privileged-users"></a>Sledování informací o systému | Privilegovaní uživatelé

**ID**: NIST SP 800-53 R4 si-4 (20) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1699 – informační systém pro sledování \| privilegovaných uživatelů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69c7bee8-bc19-4129-a51e-65a7b39d3e7c) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1699.json) |

### <a name="information-system-monitoring--unauthorized-network-services"></a>Sledování informací o systému | Neoprávněný Network Services

**ID**: NIST SP 800-53 R4 si-4 (22) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1700 – informační systém monitorování \| neautorizovaný Network Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7831b4ba-c3f4-4cb1-8c11-ef8d59438cd5) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1700.json) |

### <a name="information-system-monitoring--host-based-devices"></a>Sledování informací o systému | Zařízení založená na hostiteli

**ID**: NIST SP 800-53 R4 si-4 (23) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1701 – zařízení se systémem pro sledování informací o \| hostiteli](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff25bc08f-27cb-43b6-9a23-014d00700426) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1701.json) |

### <a name="information-system-monitoring--indicators-of-compromise"></a>Sledování informací o systému | Indikátory ohrožení zabezpečení

**ID**: NIST SP 800-53 R4 si-4 (24) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1702 – indikátory monitorování systémových informací \| o ohrožení zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4dfc0855-92c4-4641-b155-a55ddd962362) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1702.json) |

### <a name="security-alerts-advisories-and-directives"></a>Výstrahy zabezpečení, poradci a direktivy

**ID**: NIST SP 800-53 R4 si-5 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1703 – výstrahy zabezpečení, poradci a direktivy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F804faf7d-b687-40f7-9f74-79e28adf4205) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1703.json) |
|[Microsoft Managed Control 1704 – výstrahy zabezpečení, poradci a direktivy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d44b6fa-1134-4ea6-ad4e-9edb68f65429) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1704.json) |
|[Microsoft Managed Control 1705 – výstrahy zabezpečení, poradci a direktivy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff82e3639-fa2b-4e06-a786-932d8379b972) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1705.json) |
|[Microsoft Managed Control 1706 – výstrahy zabezpečení, poradci a direktivy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff475ee0e-f560-4c9b-876b-04a77460a404) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1706.json) |

### <a name="security-alerts-advisories-and-directives--automated-alerts-and-advisories"></a>Výstrahy zabezpečení, poradci a direktivy | Automatizované výstrahy a poradci

**ID**: NIST SP 800-53 R4 si-5 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1707 – výstrahy zabezpečení, poradci a direktivy \| automatizované výstrahy a poradce](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd4a2ac8-868a-4702-a345-6c896c3361ce) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1707.json) |

### <a name="security-function-verification"></a>Ověření funkce zabezpečení

**ID**: NIST SP 800-53 R4 si-6 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1708-ověření funkce zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a1e2c88-13de-4959-8ee7-47e3d74f1f48) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1708.json) |
|[Microsoft Managed Control 1709-ověření funkce zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F025992d6-7fee-4137-9bbf-2ffc39c0686c) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1709.json) |
|[Microsoft Managed Control 1710-ověření funkce zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf2a93c8-e6dd-4c94-acdd-4a2eedfc478e) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1710.json) |
|[Microsoft Managed Control 1711-ověření funkce zabezpečení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb083a535-a66a-41ec-ba7f-f9498bf67cde) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1711.json) |

### <a name="software-firmware-and-information-integrity"></a>Integrita softwaru, firmwaru a informací

**ID**: NIST SP 800-53 R4 si – 7 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1712 – integrita softwaru, firmwaru a informací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44e543aa-41db-42aa-98eb-8a5eb1db53f0) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1712.json) |

### <a name="software-firmware-and-information-integrity--integrity-checks"></a>Integrita softwaru, firmwaru a informací | Kontroly integrity

**ID**: NIST SP 800-53 R4 si-7 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1713 – kontroly integrity integrity softwaru, firmwaru a informací \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d87c70b-5012-48e9-994b-e70dd4b8def0) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1713.json) |

### <a name="software-firmware-and-information-integrity--automated-notifications-of-integrity-violations"></a>Integrita softwaru, firmwaru a informací | Automatizované oznamování porušení integrity

**ID**: NIST SP 800-53 R4 si-7 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1714 – \| Automatická oznámení týkající se narušení integrity softwaru, firmwaru a informací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe12494fa-b81e-4080-af71-7dbacc2da0ec) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1714.json) |

### <a name="software-firmware-and-information-integrity--automated-response-to-integrity-violations"></a>Integrita softwaru, firmwaru a informací | Automatizovaná reakce na porušení integrity

**ID**: NIST SP 800-53 R4 si-7 (5) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1715 – Automatická reakce na integritu softwaru, firmwaru a informací \| na porušení integrity](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd469ae0-71a8-4adc-aafc-de6949ca3339) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1715.json) |

### <a name="software-firmware-and-information-integrity--integration-of-detection-and-response"></a>Integrita softwaru, firmwaru a informací | Integrace detekce a odpovědi

**ID**: NIST SP 800-53 R4 si-7 (7) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1716 – Integrace softwaru, firmwaru a integrity informací \| o detekci a reakci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe54c325e-42a0-4dcf-b105-046e0f6f590f) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1716.json) |

### <a name="software-firmware-and-information-integrity--binary-or-machine-executable-code"></a>Integrita softwaru, firmwaru a informací | Binární a spustitelný kód počítače

**ID**: NIST SP 800-53 R4 si-7 (14) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1717 – binární soubor, firmware a integrita informací \| binární soubor nebo spustitelný kód počítače](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967773fc-d9ab-4a4e-8ff6-f5e9e3f5dbef) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1717.json) |
|[Microsoft Managed Control 1718 – binární soubor, firmware a integrita informací \| binární soubor nebo spustitelný kód počítače](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0dced7ab-9ce5-4137-93aa-14c13e06ab17) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1718.json) |

### <a name="spam-protection"></a>Ochrana před nevyžádanou poštou

**ID**: NIST SP 800-53 R4 si – 8 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1719 – ochrana před spamem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc13da9b4-fe14-4fe2-853a-5997c9d4215a) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1719.json) |
|[Microsoft Managed Control 1720 – ochrana před spamem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44b9a7cd-f36a-491a-a48b-6d04ae7c4221) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1720.json) |

### <a name="spam-protection--central-management"></a>Ochrana před spamy | Centrální správa

**ID**: NIST SP 800-53 R4 si-8 (1) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1721 – Správa ochrany před nevyžádanými zprávami \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd207aaef-7c4d-4f8c-9dce-4d62dfa3d29a) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1721.json) |

### <a name="spam-protection--automatic-updates"></a>Ochrana před spamy | Automatické aktualizace

**ID**: NIST SP 800-53 R4 si-8 (2) **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1722 – automatické aktualizace ochrany před nevyžádanou poštou \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1da06bd-25b6-4127-a301-c313d6873fff) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1722.json) |

### <a name="information-input-validation"></a>Ověření vstupu informací

**ID**: NIST SP 800-53 R4 si-10 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1723 – ověření vstupu informací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe91927a0-ac1d-44a0-95f8-5185f9dfce9f) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1723.json) |

### <a name="error-handling"></a>Zpracování chyb

**ID**: NIST SP 800-53 R4 si-11 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravovaný ovládací prvek Microsoft 1724 – zpracování chyb](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd07594d1-0307-4c08-94db-5d71ff31f0f6) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1724.json) |
|[Spravovaný ovládací prvek Microsoft 1725 – zpracování chyb](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafc234b5-456b-4aa5-b3e2-ce89108124cc) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1725.json) |

### <a name="information-handling-and-retention"></a>Manipulace s informacemi a jejich uchovávání

**ID**: NIST SP 800-53 R4 si-12 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1726 – zpracování a uchovávání informací](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbaff1279-05e0-4463-9a70-8ba5de4c7aa4) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1726.json) |

### <a name="memory-protection"></a>Ochrana paměti

**ID**: NIST SP 800-53 R4 si-16 **vlastnictví**: Shared

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1727 – ochrana paměti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F697175a7-9715-4e89-b98b-c6f605888fa3) |Společnost Microsoft implementuje tento systém a kontrolu integrity informací |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1727.json) |

> [!NOTE]
> Dostupnost konkrétních definic Azure Policy se může v Azure Government a dalších národních cloudech lišit.

## <a name="next-steps"></a>Další kroky

Další články o Azure Policy:

- Přehled [dodržování legislativních předpisů](../concepts/regulatory-compliance.md) .
- Podívejte se na [strukturu definice iniciativy](../concepts/initiative-definition-structure.md).
- Podívejte se na další příklady na [Azure Policy Samples](./index.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
