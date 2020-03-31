---
title: Zásady koncových bodů služby pro virtuální síť Azure | Microsoft Docs
description: Přečtěte si, jak používat zásady koncových bodů služby Azure Virtual Network k filtrování přenosů ve virtuální síti.
services: virtual-network
documentationcenter: na
author: RDhillon
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: 926da07ffaf0c61ca2a7fd02351ef3635ec4d73b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651286"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>Zásady koncového bodu virtuální síťové služby pro Azure Storage

Zásady koncového bodu služby Virtuální síť (Virtuální síť) umožňují filtrovat odchozí provoz virtuální sítě na účty Azure Storage přes koncový bod služby a povolit exfiltraci dat pouze na konkrétní účty Azure Storage. Zásady koncového bodu poskytují podrobné řízení přístupu pro provoz virtuální chod sítě do Azure Storage při připojování přes koncový bod služby.

![Zabezpečení odchozího provozu virtuální sítě na účty Azure Storage](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

Tato funkce je obecně dostupná pro __Azure Storage__ ve __všech globálních oblastech Azure__.

## <a name="key-benefits"></a>Klíčové výhody

Zásady koncových bodů služby pro virtuální síť nabízejí následující výhody:

- __Vylepšené zabezpečení provozu virtuální sítě do úložiště Azure__

  [Značky služeb Azure pro skupiny zabezpečení sítě](https://aka.ms/servicetags) umožňují omezit odchozí provoz virtuální sítě na konkrétní oblasti úložiště Azure. To však umožňuje provoz na libovolný účet v rámci vybrané oblasti úložiště Azure.
  
  Zásady koncového bodu umožňují určit účty Azure Storage, které mají povolený odchozí přístup virtuální sítě a omezují přístup ke všem ostatním účtům úložiště. To poskytuje mnohem podrobnější ovládací prvek zabezpečení pro ochranu exfiltrace dat z vaší virtuální sítě.

- __Škálovatelné, vysoce dostupné zásady pro filtrování přenosů u služeb Azure__

   Zásady koncových bodů nabízejí horizontálně škálovatelné, vysoce dostupné řešení, které umožňuje filtrovat přenosy dat služeb Azure ve virtuálních sítích s pomocí koncových bodů služeb. K údržbě centrálních síťových zařízení používaných v provozu virtuálních sítí tak nepotřebujete další režijní prostředky.

## <a name="json-object-for-service-endpoint-policies"></a>Zásady objektu JSON pro koncové hodslužby
Podívejme se rychle na objekt zásad koncového bodu služby.

```json
"serviceEndpointPolicyDefinitions": [
    {
            "description": null,
            "name": "MySEP-Definition",
            "resourceGroup": "MySEPDeployment",
            "service": "Microsoft.Storage",
            "serviceResources": [ 
                    "/subscriptions/subscriptionID/resourceGroups/MySEPDeployment/providers/Microsoft.Storage/storageAccounts/mystgacc"
            ],
            "type": "Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions"
    }
]
```

## <a name="configuration"></a>Konfigurace

-   Zásady koncového bodu můžete nakonfigurovat tak, aby omezily provoz virtuálních sítí na konkrétní účty Azure Storage.
-   Zásady koncových bodů jsou nakonfigurované v podsíti virtuální sítě. Koncové body služby pro Azure Storage by měly být povoleny v podsíti, aby se použila zásada.
-   Zásady koncového bodu umožňují přidat konkrétní účty Azure Storage do seznamu povolených, pomocí formátu resourceID. Přístup můžete omezit
    - všechny účty úložiště v předplatném<br>
      `E.g. /subscriptions/subscriptionId`

    - všechny účty úložiště ve skupině prostředků<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - účet individuálníúložiště výpisem odpovídající Azure Resource Manager resourceId. Tím pokryjete přenosy objektů blob, tabulek, front, souborů a Azure Data Lake Storage Gen2. <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   Ve výchozím nastavení, pokud žádné zásady jsou připojeny k podsíti s koncovými body, můžete přistupovat ke všem účtům úložiště ve službě. Jakmile v dané podsíti nakonfigurujete zásadu, budou mít k prostředkům zadaným v zásadě přístup jenom výpočetní instance dané podsítě. Přístup ke všem ostatním účtům úložiště bude odepřen.
-   Při použití zásad koncového bodu služby v podsíti se *obor koncového bodu služby* Azure Storage Service upgraduje z místní na **globální**. To znamená, že veškerý provoz do Azure Storage je zabezpečený přes koncový bod služby. Zásady koncového bodu služby jsou také použitelné globálně, takže všechny účty úložiště, které nejsou explicitně povoleny, budou odepřen přístup. 
-   V podsíti můžete použít více zásad. Pokud je k podsíti přidruženo více zásad, bude povolen provoz virtuální sítě k prostředkům určeným v rámci některé z těchto zásad. Přístup ke všem ostatním prostředkům služby, které žádná zásada neupřesňuje, bude zamítnut.

    > [!NOTE]  
    > Zásady koncového bodu služby **umožňují zásady**, takže kromě určených prostředků jsou všechny ostatní prostředky omezeny. Ujistěte se, že všechny závislosti prostředků služby pro vaše aplikace jsou identifikovány a uvedeny v zásadách.

- Zásada koncového bodu může určovat jenom účty úložiště, které používají Azure Resource Model. Vaše klasické účty Azure Storage nebudou podporovat zásady koncového bodu služby Azure.
- Pokud je uvedený primární účet, bude automaticky povolen i sekundární přístup RA-GRS.
- Účty úložiště můžou být ve stejném nebo jiném předplatném nebo v tenantovi Azure Active Directory jako virtuální síť.

## <a name="scenarios"></a>Scénáře

- **Partnerské, propojené nebo vícenásobné virtuální sítě:** Pokud chcete filtrovat přenosy v partnerských virtuálních sítích, měli byste zásady koncových bodů použít v těchto jednotlivých virtuálních sítích.
- **Filtrování internetového provozu pomocí síťových zařízení nebo brány Azure Firewall:** Filtrování provozu služeb Azure pomocí zásad, koncových bodů služby a filtrování zbytku internetu nebo provozu Azure pomocí zařízení nebo azure firewall.
- **Filtrování provozu ve službách Azure nasazených do virtuálních sítí**: V tuto chvíli nejsou zásady koncového bodu služby Azure podporované pro žádné spravované služby Azure, které se nasazují do vaší virtuální sítě. 
- **Filtrování přenosů dat z místního prostředí do služeb Azure:** Zásady koncových bodů služeb platí jenom pro přenosy z podsítí přidružených k zásadám. Pokud chcete povolit přístup k určitým prostředkům služeb Azure z místního prostředí, musíte k filtrování přenosů požít síťová virtuální zařízení nebo brány firewall.

## <a name="logging-and-troubleshooting"></a>Protokolování a řešení potíží
Zásady koncových bodů služeb nepodporují centrální přihlašování. Informace o diagnostických protokolech služby najdete v části o [protokolování koncových bodů služby](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting).

### <a name="troubleshooting-scenarios"></a>Scénáře řešení potíží
- Přístup odepřen účtům úložiště, které fungovaly ve verzi Preview (nikoli v geograficky spárované oblasti)
  - S upgradem služby Azure Storage na použití globálních značek služeb je teď globální obor koncového bodu služby a tedy zásady koncového bodu služby. Takže všechny přenosy do Služby Azure Storage je šifrovaný přes koncové body služby a pouze účty úložiště, které jsou explicitně uvedené v zásadách jsou povolen přístup.
  - Explicitně povolit seznam všech požadovaných účtů úložiště k obnovení přístupu.  
  - Kontaktujte podporu Azure.
- Účty uvedené v zásadách koncových bodů mají zakázaný přístup
  - Přístup mohou blokovat skupiny zabezpečení sítě nebo filtry brány firewall.
  - Pokud odebrání nebo opětovné použití zásady vede ke ztrátě připojení:
    - Ověřte, jestli je služba Azure nakonfigurovaná tak, aby umožňovala přístup z virtuální sítě přes koncové body, nebo jestli je výchozí zásada pro prostředek nastavená na *Povolit vše*.
    - Ověřte, jestli diagnostika služby zobrazuje provoz v koncových bodech.
    - Zkontrolujte, jestli se v protokolech toků skupin zabezpečení sítě zobrazuje očekávaný přístup nebo jestli se zobrazuje v protokolech úložišť (v koncových bodech služby).
    - Kontaktujte podporu Azure.
- Přístup se zamítne účtům, které nejsou uvedené v zásadách koncového bodu služby
  - Ověřte, jestli je Azure Storage nakonfigurovaný tak, aby umožňoval přístup z virtuální sítě přes koncové body, nebo jestli je výchozí zásada pro prostředek nastavená na *Povolit vše*.
  - Ujistěte se, že účty nejsou **klasické účty úložiště** se zásadami koncového bodu služby v podsíti.
- Spravovaná služba Azure přestala fungovat po použití zásad koncového bodu služby v podsíti
  - Spravované služby nejsou v současné době podporovány zásadami koncového bodu služby. *Sledujte tento prostor pro aktualizace*.

## <a name="provisioning"></a>Zřizování

Zásady koncového bodu služby může v podsítích nakonfigurovat uživatel, který má do virtuální sítě přístup pro zápis. Další informace o [předdefinovaných rolích](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure a přiřazení konkrétních oprávnění k [vlastním rolím](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuální sítě a účty Azure Storage můžou být ve stejném nebo v různých předplatných nebo v tenantech Azure Active Directory.

## <a name="limitations"></a>Omezení

- Zásady koncového bodu služby můžete nasadit jenom ve virtuálních sítích nasazených prostřednictvím modelu Azure Resource Manageru.
- Virtuální sítě musí být ve stejné oblasti jako zásada koncového bodu služby.
- Zásadu koncového bodu služby můžete v podsíti použít, jen když jsou koncové body služby nakonfigurované pro služby Azure uvedené v zásadě.
- Zásady koncových bodů služeb nemůžete použít pro přenosy dat z místní sítě do služeb Azure.
- Spravované služby Azure aktuálně nepodporují zásady koncového bodu. To zahrnuje spravované služby nasazené do sdílených podsítí (například *Azure HDInsight, Azure Batch, Azure ADDS, Azure APplication Gateway, Brána Azure VPN, Azure Firewall)* nebo do vyhrazených podsítí (např. *Azure App Service Environment, Azure Redis Cache, Azure API Management, Azure SQL MI, klasické spravované služby).*

 > [!WARNING]
 > Služby Azure nasazené ve vaší virtuální síti, jako je Azure HDInsight, přistupují k jiným službám Azure, jako je Azure Storage, kvůli požadavkům na infrastrukturu. Pokud zásadou koncového bodu omezíte přístup jenom k určitým prostředkům, můžete u služeb Azure nasazených ve své virtuální síti rozdělit přístup k těmto prostředkům infrastruktury.

- Klasické účty úložiště nejsou podporovány v zásadách koncového bodu. Zásady automaticky zamítnou přístup všem klasickým účtům úložiště. Pokud vaše aplikace potřebuje přístup k Azure Resource Manageru a klasickým účtům úložišť, nepoužívejte pro tyto přenosy zásady koncových bodů.

## <a name="pricing-and-limits"></a>Ceny a omezení

Za používání zásad koncových bodů služby se neplatí další poplatky. Pro služby Azure (jako je Azure Storage) poskytované přes koncové body služeb platí aktuální cenový model.

Pro zásady koncových bodů služeb platí následující limity: 

 |Prostředek | Výchozí omezení |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Další kroky

- Další informace o [konfiguraci zásad koncových bodů služeb virtuální sítě](virtual-network-service-endpoint-policies-portal.md)
- Další informace o [koncových bodech služby virtuální sítě](virtual-network-service-endpoints-overview.md)
