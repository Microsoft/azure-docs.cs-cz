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
ms.openlocfilehash: 633d0b9e454fe2906d98624ac2cdbcc9a8b6c7fa
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022258"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>Zásady koncového bodu služby pro virtuální síť pro službu Azure Storage

Zásady koncového bodu služby Virtual Network (VNet) umožňují filtrovat odchozí přenosy virtuální sítě na účty Azure Storage prostřednictvím koncového bodu služby a exfiltraceovat data pouze na konkrétní Azure Storage účty. Zásady koncového bodu poskytují podrobné řízení přístupu pro provoz virtuální sítě Azure Storage při připojování přes koncový bod služby.

![Zabezpečení odchozího provozu virtuální sítě do Azure Storage účtů](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

Tato funkce je všeobecně dostupná pro __Azure Storage__ ve __všech globálních oblastech Azure__.

## <a name="key-benefits"></a>Klíčové výhody

Zásady koncových bodů služby pro virtuální síť nabízejí následující výhody:

- __Vylepšené zabezpečení Virtual Network provozu do Azure Storage__

  [Značky služeb Azure pro skupiny zabezpečení sítě](./network-security-groups-overview.md) umožňují omezit odchozí přenosy virtuální sítě do konkrétních oblastí Azure Storage. To však umožňuje provoz do libovolného účtu v rámci vybrané Azure Storage oblasti.
  
  Zásady koncového bodu umožňují zadat Azure Storage účty, kterým je povolen odchozí přístup k virtuální síti, a omezit přístup ke všem ostatním účtům úložiště. To poskytuje mnohem podrobnější kontrolu zabezpečení pro ochranu datových exfiltrace z vaší virtuální sítě.

- __Škálovatelné, vysoce dostupné zásady pro filtrování přenosů u služeb Azure__

   Zásady koncových bodů nabízejí horizontálně škálovatelné, vysoce dostupné řešení, které umožňuje filtrovat přenosy dat služeb Azure ve virtuálních sítích s pomocí koncových bodů služeb. K údržbě centrálních síťových zařízení používaných v provozu virtuálních sítí tak nepotřebujete další režijní prostředky.

## <a name="json-object-for-service-endpoint-policies"></a>Objekt JSON pro zásady koncového bodu služby
Pojďme se rychle podívat na objekt zásad koncového bodu služby.

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

-   Zásady koncového bodu můžete nakonfigurovat tak, aby se omezil provoz virtuální sítě na konkrétní účty Azure Storage.
-   Zásady koncových bodů jsou nakonfigurované v podsíti virtuální sítě. V podsíti by měly být povoleny koncové body služby pro Azure Storage, aby bylo možné zásady použít.
-   Zásady koncového bodu umožňují přidat konkrétní účty Azure Storage do seznamu povolených pomocí formátu resourceID. Můžete omezit přístup k
    - všechny účty úložiště v předplatném<br>
      `E.g. /subscriptions/subscriptionId`

    - všechny účty úložiště ve skupině prostředků<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - jednotlivé účty úložiště výpisem odpovídajících Azure Resource Manager resourceId. Tím pokryjete přenosy objektů blob, tabulek, front, souborů a Azure Data Lake Storage Gen2. <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   Ve výchozím nastavení platí, že pokud nejsou k podsíti s koncovými body připojeny žádné zásady, budete mít přístup ke všem účtům úložiště ve službě. Jakmile v dané podsíti nakonfigurujete zásadu, budou mít k prostředkům zadaným v zásadě přístup jenom výpočetní instance dané podsítě. Přístup ke všem ostatním účtům úložiště bude odepřen.
-   Při aplikování zásad koncového bodu služby v podsíti se *obor koncového bodu služby* Azure Storage upgraduje z místního na **globální**. To znamená, že veškerý provoz, který se Azure Storage, je po skončení koncového bodu služby zabezpečený. Zásady koncového bodu služby se taky použijí globálně, takže všechny účty úložiště, které nejsou explicitně povolené, budou mít odepřený přístup. 
-   V podsíti můžete použít více zásad. Pokud je k podsíti přidruženo více zásad, bude povolen provoz virtuální sítě do prostředků zadaných v kterékoli z těchto zásad. Přístup ke všem ostatním prostředkům služby, které žádná zásada neupřesňuje, bude zamítnut.

    > [!NOTE]  
    > Zásady koncového bodu služby **umožňují zásady**, a to od zadaných zdrojů, ale všechny ostatní prostředky jsou omezené. Zajistěte, aby všechny závislosti prostředků služby pro vaše aplikace byly identifikované a uvedené v zásadách.

- Zásada koncového bodu může určovat jenom účty úložiště, které používají Azure Resource Model. Účty klasického Azure Storage nebudou podporovat zásady koncového bodu služby Azure.
- Pokud je uvedený primární účet, bude automaticky povolen i sekundární přístup RA-GRS.
- Účty úložiště můžou být ve stejném nebo jiném předplatném nebo v tenantovi Azure Active Directory jako virtuální síť.

## <a name="scenarios"></a>Scénáře

- **Partnerské, propojené nebo vícenásobné virtuální sítě:** Pokud chcete filtrovat přenosy v partnerských virtuálních sítích, měli byste zásady koncových bodů použít v těchto jednotlivých virtuálních sítích.
- **Filtrování internetového provozu pomocí síťových zařízení nebo Azure firewall**: filtrovat provoz služeb Azure pomocí zásad, nad koncovými body služby a filtrovat zbývající data z Internetu nebo Azure přes zařízení nebo Azure firewall.
- **Filtrování provozu ve službách Azure nasazených do virtuálních sítí**: v současné době se zásady koncového bodu služby Azure nepodporují pro žádné spravované služby Azure nasazené do vaší virtuální sítě. 
- **Filtrování přenosů dat z místního prostředí do služeb Azure:** Zásady koncových bodů služeb platí jenom pro přenosy z podsítí přidružených k zásadám. Pokud chcete povolit přístup k určitým prostředkům služeb Azure z místního prostředí, musíte k filtrování přenosů požít síťová virtuální zařízení nebo brány firewall.

## <a name="logging-and-troubleshooting"></a>Protokolování a řešení potíží
Zásady koncových bodů služeb nepodporují centrální přihlašování. Protokoly prostředků služby najdete v tématu [protokolování koncových bodů služby](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting).

### <a name="troubleshooting-scenarios"></a>Scénáře řešení potíží
- Byl odepřen přístup k účtům úložiště, které pracovaly ve verzi Preview (ne v geograficky spárované oblasti).
  - Díky Azure Storage upgradu na používání globálních značek služeb je teď obor koncového bodu služby a zásady koncového bodu služby globální. Veškerý provoz Azure Storage je zašifrovaný přes koncové body služby a přístup k nim mají jenom účty úložiště, které jsou v zásadách výslovně uvedené.
  - Explicitně povolte seznam všech požadovaných účtů úložiště pro obnovení přístupu.  
  - Kontaktujte podporu Azure.
- Účty uvedené v zásadách koncových bodů mají zakázaný přístup
  - Přístup mohou blokovat skupiny zabezpečení sítě nebo filtry brány firewall.
  - Pokud odebrání nebo opětovné použití zásady vede ke ztrátě připojení:
    - Ověřte, jestli je služba Azure nakonfigurovaná tak, aby povolovala přístup z virtuální sítě přes koncové body, nebo jestli je výchozí zásada pro prostředek nastavená na *povoleno*.
    - Ověřte, jestli diagnostika služby zobrazuje provoz v koncových bodech.
    - Zkontrolujte, jestli se v protokolech toků skupin zabezpečení sítě zobrazuje očekávaný přístup nebo jestli se zobrazuje v protokolech úložišť (v koncových bodech služby).
    - Kontaktujte podporu Azure.
- Přístup se zamítne účtům, které nejsou uvedené v zásadách koncového bodu služby
  - Ověřte, jestli je Azure Storage nakonfigurovaná tak, aby povolovala přístup z virtuální sítě přes koncové body, nebo jestli je výchozí zásada pro prostředek nastavená na *povoleno*.
  - Ujistěte se, že účty nejsou **klasické účty úložiště** se zásadami koncového bodu služby v podsíti.
- Spravovaná služba Azure po použití zásad koncového bodu služby v podsíti přestala pracovat.
  - V tuto chvíli se v zásadách koncového bodu služby nepodporují spravované služby. *Podívejte se na toto místo pro aktualizace*.
- Přístup ke spravovaným účtům úložiště přestal po použití zásad koncového bodu služby v podsíti fungovat.
  - Spravované účty úložiště nejsou u zásad koncového bodu služby podporované. V případě nakonfigurované zásady budou ve výchozím nastavení Odepřít přístup ke všem spravovaným účtům úložiště. Pokud vaše aplikace potřebuje přístup ke spravovaným účtům úložiště, neměly by se pro tento provoz používat zásady koncového bodu.

## <a name="provisioning"></a>Zřizování

Zásady koncového bodu služby může v podsítích nakonfigurovat uživatel, který má do virtuální sítě přístup pro zápis. Další informace o [předdefinovaných rolích](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure a přiřazení konkrétních oprávnění k [vlastním rolím](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuální sítě a účty Azure Storage můžou být ve stejném nebo různých předplatných nebo Azure Active Directory klienty.

## <a name="limitations"></a>Omezení

- Zásady koncového bodu služby můžete nasadit jenom ve virtuálních sítích nasazených prostřednictvím modelu Azure Resource Manageru.
- Virtuální sítě musí být ve stejné oblasti jako zásada koncového bodu služby.
- Zásadu koncového bodu služby můžete v podsíti použít, jen když jsou koncové body služby nakonfigurované pro služby Azure uvedené v zásadě.
- Zásady koncových bodů služeb nemůžete použít pro přenosy dat z místní sítě do služeb Azure.
- Spravované služby Azure v současné době nepodporují zásady koncového bodu. To zahrnuje spravované služby nasazené do sdílených podsítí (např. *Azure Batch, Azure, azure Application Gateway, azure VPN Gateway, Azure firewall*) nebo do vyhrazených podsítí (např. *Azure App Service Environment, Azure Redis Cache, Azure API Management, Azure SQL mi, klasických spravovaných služeb*).

 > [!WARNING]
 > Služby Azure nasazené ve vaší virtuální síti, jako je Azure HDInsight, přistupují k jiným službám Azure, jako je Azure Storage, kvůli požadavkům na infrastrukturu. Pokud zásadou koncového bodu omezíte přístup jenom k určitým prostředkům, můžete u služeb Azure nasazených ve své virtuální síti rozdělit přístup k těmto prostředkům infrastruktury.

- Klasické účty úložiště nejsou v zásadách koncového bodu podporovány. Zásady automaticky zamítnou přístup všem klasickým účtům úložiště. Pokud vaše aplikace potřebuje přístup k Azure Resource Manageru a klasickým účtům úložišť, nepoužívejte pro tyto přenosy zásady koncových bodů.

## <a name="pricing-and-limits"></a>Ceny a omezení

Za používání zásad koncových bodů služby se neplatí další poplatky. Pro služby Azure (jako je Azure Storage) poskytované přes koncové body služeb platí aktuální cenový model.

Pro zásady koncových bodů služeb platí následující limity: 

 |Prostředek | Výchozí omezení |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpointPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Další kroky

- Další informace o [konfiguraci zásad koncových bodů služeb virtuální sítě](virtual-network-service-endpoint-policies-portal.md)
- Další informace o [koncových bodech služby virtuální sítě](virtual-network-service-endpoints-overview.md)
