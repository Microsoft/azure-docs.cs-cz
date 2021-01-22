---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a66976898dc30a02d51ef4a7d992a0603c6db43e
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684724"
---
## <a name="azure-security-benchmark-v1"></a>Srovnávací test zabezpečení Azure v1

[Srovnávací test zabezpečení Azure](../../../../articles/security/benchmarks/overview.md) poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure. Informace o tom, jak tato služba zcela namapuje srovnávací test zabezpečení Azure, najdete v [souborech mapování srovnávacích testů zabezpečení Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – Azure Security test](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásady<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Ochrana dat |4.4 |Šifrování všech citlivých informací během přenosu |[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – HIPAA HITRUST 9,2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Další informace o tomto standardu dodržování předpisů najdete v tématu [HIPAA HITRUST 9,2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásady<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Řízení připojení k síti |0809.01 n2Organizational. 1234-01. n |Síťový provoz se řídí v souladu se zásadami řízení přístupu organizací přes bránu firewall a dalšími omezeními týkajícími se sítě pro každý přístupový bod sítě nebo spravované rozhraní externí telekomunikační služby. |[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Řízení připojení k síti |0810.01 n2Organizational. 5 – 01. n |Přenesené informace jsou zabezpečené a jsou ve srovnání s otevřenými veřejnými sítěmi alespoň šifrované. |[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Řízení připojení k síti |0811.01 n2Organizational. 6 – 01. n |Výjimky ze zásad toku provozu jsou popsány s podporou mise/obchodní potřeby, trvání výjimky a nejméně jednou ročně; výjimky zásad toku přenosů se odeberou, pokud už nejsou podporované explicitním posláním nebo obchodním potřebám. |[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Řízení připojení k síti |0812.01 n2Organizational. 8 – 01. n |Vzdálená zařízení, která vytvářejí nevzdálené připojení, nemají povolenou komunikaci s externími (vzdálenými) prostředky. |[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Řízení připojení k síti |0814.01 n1Organizational. 12.01. n |Schopnost uživatelů připojit se k interní síti je omezená pomocí zásad pro řízení přístupu, které jsou ve spravovaných rozhraních a jsou v souladu se zásadami řízení přístupu, a požadavky klinických a obchodních aplikací. |[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Identifikace rizik souvisejících s externími stranami |1451.05 iCSPOrganizational. 2.5. i |Poskytovatelé cloudových služeb navrhují a implementují ovládací prvky, které umožňují zmírnit a zahrnovat rizika zabezpečení dat prostřednictvím správného oddělení povinností, přístupu na základě role a přístupu k minimálnímu oprávnění pro všechny zaměstnance v rámci jejich dodavatelského řetězce. |[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Transakce na řádku |0946.09 y2Organizational. 14 – 09. y |Organizace vyžaduje použití šifrování mezi a používání elektronických podpisů každou ze smluvních stran, které jsou zapojeny do transakce. |[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md).
Další informace o tomto standardu dodržování předpisů najdete v tématu [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásady<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Ochrana systémů a komunikací |3.13.1 |Monitorujte, kontrolujte a Zabezpečte komunikaci (tj. informace přenášené nebo přijímané organizačními systémy) na vnějších hranicích a na základních vnitřních hranicích systémů organizace. |[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Ochrana systémů a komunikací |3.13.8 |Implementujte kryptografické mechanismy, aby se zabránilo neoprávněnému odhalení CUI během přenosu, pokud není jinak chráněná alternativním fyzickým zabezpečením. |[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Další informace o tomto standardu dodržování předpisů najdete v tématu [NIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásady<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Ochrana systémů a komunikací |SC-8 (1) |Důvěrnost přenosů a \| kryptografická integrita nebo alternativní fyzická ochrana |[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

