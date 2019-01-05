---
title: Zásady koncových bodů služby Azure Virtual Network | Microsoft Docs
description: Přečtěte si, jak používat zásady koncových bodů služby Azure Virtual Network k filtrování přenosů ve virtuální síti.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sumeet.mittal
ms.openlocfilehash: 7a3a94e9759dfb3c525ffcf1e840d5bec18f4808
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051307"
---
# <a name="virtual-network-service-endpoint-policies-preview"></a>Zásady koncových bodů služby Virtual Network (Preview)

Zásady koncových bodů služby Virtual Network (VNet) umožňují v provozu virtuální sítě filtrovat služby Azure, aby ke koncovým bodům služby měly přístup jenom určité prostředky služeb Azure. Zásady koncových bodů umožňují v provozu virtuální sítě podrobně řídit přístup ke službám Azure.

Tato funkce je dostupná ve verzi __Preview__ pro následující služby a oblasti Azure:

__Azure Storage__: WestCentralUS, WestUS2.

Aktuální oznámení o verzi Preview najdete na stránce s [aktuálními informacemi o službě Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

> [!NOTE]  
> Po dobu verze Preview nemusejí mít zásady koncových bodů služby virtuální sítě stejnou dostupnost a spolehlivost jako funkce vydané v obecně dostupné verzi. Další informace najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-benefits"></a>Klíčové výhody

Zásady koncových bodů služby virtuální sítě nabízejí následující výhody:

- __Vylepšené zabezpečení provozu virtuální sítě pro služby Azure__

  [Značky služeb Azure skupin zabezpečení sítě](https://aka.ms/servicetags) umožňují omezit ve virtuální síti odchozí přenosy na určité služby Azure. Tím ale také umožňují přenosy všem prostředkům dané služby Azure. 
  
  Použitím zásad koncových bodů teď můžete ve virtuální síti omezit odchozí přístup jenom na určité prostředky Azure. Můžete tak mnohem podrobněji řídit zabezpečení a chránit data, ke kterým se přistupuje ve vaší virtuální síti. 

- __Škálovatelné, vysoce dostupné zásady pro filtrování přenosů u služeb Azure__

   Zásady koncových bodů nabízejí horizontálně škálovatelné, vysoce dostupné řešení, které umožňuje filtrovat přenosy dat služeb Azure ve virtuálních sítích s pomocí koncových bodů služeb. K údržbě centrálních síťových zařízení používaných v provozu virtuálních sítí tak nepotřebujete další režijní prostředky.

## <a name="configuration"></a>Konfigurace

- Zásady koncových bodů můžete nakonfigurovat tak, aby byl provoz virtuální sítě omezen jenom na určité prostředky služeb Azure. Ve verzi Preview podporujeme zásady koncových bodů pro Azure Storage. 
- Zásady koncových bodů jsou nakonfigurované v podsíti virtuální sítě. Aby zásada platila pro všechny služby Azure, které jsou v ní uvedené, musí být koncové body služby povolené v podsíti.
- Zásada koncového bodu umožňuje přidat na seznam povolených prostředků určité prostředky služeb Azure ve formátu resourceID. Můžete omezit přístup ke všem prostředkům v předplatném nebo skupině prostředků. Nebo můžete omezit přístup jenom k určitým prostředkům. 
- Ve výchozím nastavení nemá podsíť s koncovými body připojené žádné zásady, takže máte přístup ke všem prostředkům ve službě. Jakmile v dané podsíti nakonfigurujete zásadu, budou mít k prostředkům zadaným v zásadě přístup jenom výpočetní instance dané podsítě. Přístup ke všem dalším prostředkům dané služby bude zamítnut. 
- Přenos dat k prostředkům služeb Azure můžete filtrovat v oblastech, kde je nakonfigurovaný koncový bod služby. Přístup k prostředkům služby v jiných oblastech bude automaticky povolen. 

  > [!NOTE]  
  > Pokud chcete ve virtuální síti úplně zamknout odchozí přístup k prostředkům Azure v oblastech s koncovými body služby, musíte také nakonfigurovat pravidla skupiny zabezpečení sítě tak, aby povolovala provoz jenom v oblastech s koncovými body služby pomocí [značek služby](security-overview.md#service-tags).

- V podsíti můžete použít více zásad. Pokud má podsíť přiřazených více zásad, které se týkají stejné služby, budou ve virtuální síti povoleny přenosy dat k prostředkům zadaným v kterékoli z těchto zásad. Přístup ke všem ostatním prostředkům služby, které žádná zásada neupřesňuje, bude zamítnut. 

  > [!NOTE]  
  > Zásada umožňuje přístup z virtuální sítě jenom k uvedeným prostředkům služby. Pokud do zásady přidáte určité prostředky, budou veškeré ostatní přenosy do služby automaticky zamítnuty. Ověřte, že zásada určuje a vyjmenovává všechny závislosti prostředků služeb pro vaše aplikace.

  > [!WARNING]  
  > Služby Azure nasazené ve vaší virtuální síti, jako je Azure HDInsight, přistupují k jiným službám Azure, jako je Azure Storage, kvůli požadavkům na infrastrukturu. Pokud zásadou koncového bodu omezíte přístup jenom k určitým prostředkům, můžete u služeb nasazených ve své virtuální síti rozdělit přístup k těmto prostředkům infrastruktury. Informace o konkrétních službách viz [Omezení](#limitations). Ve verzi Preview nejsou zásady koncových bodů služeb podporované u spravovaných služeb Azure nasazených ve vaší virtuální síti.

- Azure Storage: 
  -  Přístup omezíte tak, že uvedete *resourceId* Azure Resource Manageru pro účet úložiště. Tím pokryjete přenosy objektů blob, tabulek, front, souborů a Azure Data Lake Storage Gen2.

     V definici můžou být pro účty Azure Storage uvedené například tyto zásady koncových bodů:
    
     Povolení určitého účtu úložiště:         
     `subscriptions/subscriptionId/resourceGroups/resourceGroup/providers/Microsoft.Storage/storageAccounts/storageAccountName`
      
     Povolení všech účtů v předplatném a ve skupině prostředků: `/subscriptions/subscriptionId/resourceGroups/resourceGroup`
     
     Povolení všech účtů v předplatném: `/subscriptions/subscriptionId`
    
- Zásada koncového bodu může určovat jenom účty úložiště, které používají Azure Resource Model.  

  > [!NOTE]  
  > Zásady koncových bodů blokují přístup klasickým účtům úložišť.

- Primární umístění uvedeného účtu musí být v geograficky párovaných oblastech koncového bodu služby pro danou podsíť. 

  > [!NOTE]  
  > Zásady umožňují zadat prostředky služby z jiných oblastí. Ve virtuální síti se přístup ke službám Azure filtruje jenom pro geograficky párované oblasti. Pokud skupiny zabezpečení sítě nejsou omezené na geograficky párované oblasti služby Azure Storage, má virtuální síť přístup ke všem účtům úložiště mimo geograficky párované oblasti.

- Pokud je uvedený primární účet, bude automaticky povolen i sekundární přístup RA-GRS. 
- Účty úložiště můžou být ve stejném nebo jiném předplatném nebo v tenantovi Azure Active Directory jako virtuální síť. 

## <a name="limitations"></a>Omezení

- Zásady koncového bodu služby můžete nasadit jenom ve virtuálních sítích nasazených prostřednictvím modelu Azure Resource Manageru.
- Virtuální sítě musí být ve stejné oblasti jako zásada koncového bodu služby.
- Zásadu koncového bodu služby můžete v podsíti použít, jen když jsou koncové body služby nakonfigurované pro služby Azure uvedené v zásadě.
- Zásady koncových bodů služeb nemůžete použít pro přenosy dat z místní sítě do služeb Azure.
- Zásady koncových bodů se nesmí používat v podsítích se spravovanými službami Azure, které mají požadavky na infrastrukturu závisející na službách Azure. 

  > [!WARNING]  
  > Služby Azure nasazené ve vaší virtuální síti, jako je Azure HDInsight, přistupují k jiným službám Azure, jako je Azure Storage, kvůli požadavkům na infrastrukturu. Pokud zásadou koncového bodu omezíte přístup jenom k určitým prostředkům, můžete u služeb Azure nasazených ve své virtuální síti rozdělit přístup k těmto prostředkům infrastruktury.
  
  - Některé služby Azure můžete nasadit v podsítích s dalšími výpočetními instancemi. Zajistěte, aby se v podsíti nepoužívaly zásady koncových bodů, pokud jsou v ní nasazené následující spravované služby.
   
    - Azure HDInsight
    - Azure Batch (Azure Resource Manager)
    - Azure Active Directory Domain Services (Azure Resource Manager)
    - Azure Application Gateway (Azure Resource Manager)
    - Azure VPN Gateway (Azure Resource Manager)
    - Brána Azure Firewall

  - Některé služby Azure jsou nasazené ve vyhrazených podsítích. Ve verzi Preview jsou zásady koncových bodů zablokované u všech následujících služeb: 

     - Azure App Service Environment
     - Azure Rediscache
     - Azure API Management
     - Spravovaná instance Azure SQL
     - Azure Active Directory Domain Services
     - Azure Application Gateway (Classic)
     - Azure VPN Gateway (Classic)

- Azure Storage: Klasických účtů úložiště nejsou podporované v zásadách koncového bodu. Zásady automaticky zamítnou přístup všem klasickým účtům úložiště. Pokud vaše aplikace potřebuje přístup k Azure Resource Manageru a klasickým účtům úložišť, nepoužívejte pro tyto přenosy zásady koncových bodů. 

## <a name="nsgs-with-service-endpoint-policies"></a>Skupiny zabezpečení sítě se zásadami koncového bodu služby
- Ve výchozím nastavení povolují skupiny zabezpečení sítě odchozí internetové přenosy, včetně přenosů dat z virtuální sítě do služeb Azure.
- Pokud chcete zakázat všechny odchozí internetové přenosy a povolit přenosy jenom určitým prostředkům služeb Azure: 

  Krok 1: Konfigurace skupin zabezpečení sítě umožňující odchozí provoz jenom na služby Azure v oblastech koncový bod pomocí *značek služeb Azure*. Další informace najdete v části o [značkách služeb pro skupiny zabezpečení sítě](https://aka.ms/servicetags).
      
  Například pravidla skupiny zabezpečení sítě, která omezují přístup jenom na oblasti s koncovými body, vypadají následovně:

  ```
  Allow AzureStorage.WestUS2,
  Allow AzureStorage.WestCentralUS,
  Deny all
  ```

  Krok 2: Zásady koncového bodu služby s přístupem platí pro pouze prostředky konkrétních služeb Azure.

  > [!WARNING]  
  > Pokud konfigurace skupiny zabezpečení sítě neomezuje přístup virtuální sítě ke službě Azure na oblasti s koncovými body, můžete přistupovat k prostředkům služby i v jiných oblastech, přestože platí zásada koncového bodu služby.

## <a name="scenarios"></a>Scénáře

- **Partnerské, propojené nebo vícenásobné virtuální sítě**: Pro filtrování provozu ve virtuálních sítích s navázaným partnerským vztahem, zásad koncových bodů bude použito jednotlivě na tyto virtuální sítě.
- **Filtrování přenosy z Internetu pomocí síťových zařízení nebo brány Firewall Azure**: Filtrovat provoz služeb Azure se zásadami, prostřednictvím koncových bodů a filtrovat rest z Internetu nebo v Azure provoz přes zařízení nebo brána Firewall služby Azure. 
- **Filtrování provozu služeb Azure, které jsou nasazené do virtuálních sítí**: Ve verzi preview nejsou podporované zásadami koncových bodů služeb pro služby spravované Azure, které jsou nasazeny do vaší virtuální sítě. 
 Informace o konkrétních službách najdete v části [Omezení](#Limitations).
- **Filtrování provozu do služby Azure z místního**: Zásady koncového bodu služby platí jenom pro provoz z podsítě přidružené zásady. Pokud chcete povolit přístup k určitým prostředkům služeb Azure z místního prostředí, musíte k filtrování přenosů požít síťová virtuální zařízení nebo brány firewall.

## <a name="logging-and-troubleshooting"></a>Protokolování a řešení potíží
Zásady koncových bodů služeb nepodporují centrální přihlašování. Informace o diagnostických protokolech služby najdete v části o [protokolování koncových bodů služby](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting).

### <a name="troubleshooting-scenarios"></a>Scénáře řešení potíží
- Povolený přístup k účtům úložiště není uvedený v zásadách koncového bodu
  - Skupiny zabezpečení sítě můžou povolovat přístup k internetu nebo k účtům Azure Storage v jiných oblastech.
  - Skupiny zabezpečení sítě by měly být nakonfigurovány tak, aby zakazovaly veškeré odchozí internetové přenosy a povolovaly jenom přenosy do určitých oblastí Azure Storage. Podrobné informace viz [Skupiny zabezpečení sítě](#network-security-groups).
- Účty uvedené v zásadách koncových bodů mají zakázaný přístup
  - Přístup mohou blokovat skupiny zabezpečení sítě nebo filtry brány firewall.
  - Pokud odebrání nebo opětovné použití zásady vede ke ztrátě připojení:
   - Ověřte, jestli je služba Azure nakonfigurovaná tak, aby povolovala přístup z virtuální sítě přes koncové body, nebo že je výchozí zásada pro prostředek nastavená na *Povolit vše*.
      > [!NOTE]      
      > Prostředky služeb nemusí být připojené k virtuálním sítím, aby byl možný přístup prostřednictvím zásad koncových bodů. Jako osvědčený bezpečný postup ale doporučujeme, aby prostředky služby byly připojené k důvěryhodným sítím, jako jsou virtuální sítě Azure, prostřednictvím koncových bodů služeb a v místním prostředí prostřednictvím IP firewallu.
  
   - Ověřte, jestli diagnostika služby zobrazuje provoz v koncových bodech.
    - Zkontrolujte, jestli se v protokolech toků skupin zabezpečení sítě zobrazuje očekávaný přístup nebo jestli se zobrazuje v protokolech úložišť (v koncových bodech služby).
    - Kontaktujte podporu Azure.
- Přístup se zamítne účtům, které nejsou uvedené v zásadách koncového bodu služby
  - Přístup mohou blokovat filtry skupin zabezpečení sítě nebo brány firewall. Ověřte, že je v oblastech s koncovými body povolená značka služby *Azure Storage*. Informace o omezeních zásad najdete v části [Omezení](#limitations).
  Pokud je použitá zásada, mají zakázaný přístup klasické účty úložiště.
  - Ověřte, jestli je služba Azure nakonfigurovaná tak, aby povolovala přístup z virtuální sítě přes koncové body, nebo jestli je výchozí zásada prostředku nastavená na *Povolit vše*.

## <a name="provisioning"></a>Zřizování

Zásady koncového bodu služby může v podsítích nakonfigurovat uživatel, který má do virtuální sítě přístup pro zápis. Další informace o [předdefinovaných rolích](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure a přiřazení konkrétních oprávnění k [vlastním rolím](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuální sítě a prostředky služby Azure můžou být ve stejném předplatném nebo v různých předplatných či tenantech Azure Active Directory. 

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
- Další informace o [koncových bodech služeb virtuální sítě](virtual-network-service-endpoints-overview.md)

