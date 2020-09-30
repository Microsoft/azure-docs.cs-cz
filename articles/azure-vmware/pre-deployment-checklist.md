---
title: Kontrolní seznam předběžného nasazení řešení Azure VMware
description: Tento kontrolní seznam použijte jako součást procesu plánování před nasazením.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 51480f72d6de6b1f49aa753e006c4683bbd48cc7
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579765"
---
# <a name="azure-vmware-solution-pre-deployment-checklist"></a>Kontrolní seznam předběžného nasazení řešení Azure VMware
Tento kontrolní seznam před nasazením použijete během [plánovací fáze](production-ready-deployment-steps.md).

## <a name="success-criteria"></a>Kritéria úspěchu
Definujte, které testy se budou spouštět, a očekávaný výsledek.

| Základní informace potřeba | Vaše odpověď |
| ----------- | ------------- |
| Nasazení SDDC | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Vytvoření virtuální sítě | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Vytvořit pole pro skok | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Vytvořit bránu virtuální sítě | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Připojit Global Reach ExpressRoute | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Přihlášení k NSX Manageru a vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| Nutné primární informace | Vaše odpověď |
| --------| --------------|
| Vytvořit server DHCP | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Vytvoření segmentů sítě | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Škálovat (Přidat nebo odstranit uzly) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Nasazení VMware HCX | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Vytváření virtuálních počítačů (VM) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
| Povolit Internet | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Migrace virtuálních počítačů z místního do privátního cloudu | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| Další potřebné informace | Vaše odpověď |
| --------| --------------|
| Operace snímků virtuálních počítačů | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Nasazení nástroje pro vyrovnávání zatížení NSX-T | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Integrace Azure<br><ul><li>Sdílená knihovna obsahu</li><li>Integrace zabezpečení</li><li>Odeslat ISO</li><li>Sestavení z ISO</li><li>Azure Backup</li></ul> | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Micro segmentace | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Směrování | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |




## <a name="azure-vmware-solution-information"></a>Informace o řešení Azure VMware

#### <a name="azure-subscription"></a>Předplatné Azure
Zadejte název předplatného a ID předplatného pro řešení Azure VMware. Předplatné může být nové nebo existující předplatné. Nepoužívejte předplatné pro vývoj/testování.

| Potřebné informace  | Vaše odpověď |
| ------------| ------------- |
| Předplatné a ID | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Předplatné s EA | ☐ Ano &nbsp; &nbsp; ☐ ne  |
| Název skupiny prostředků | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Umístění | ☒ Východní USA |
| Správce Azure<br><br>Zadejte jméno a kontakt správce.<br>přiřazeno pro povolení služby z Marketplace.<br>Přispěvatel je minimální role požadovaná pro <br>[Zaregistrujte poskytovatele prostředků řešení Azure VMware](tutorial-create-private-cloud.md#register-the-resource-provider). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="on-premises-vmware-information"></a>Místní informace VMware

| Potřebné informace  | Vaše odpověď |
| ------------| --------------|
| verze vSphere | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| verze vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Správce vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Rozšíření L2<br><br>Pokud rozšiřujete sítě L2 pomocí VMware HCX,<br>Zadejte místní síť, kterou budete rozšířeni. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Virtuálního<br><br>Zadejte typ virtuálního přepínače použitý v celém prostředí. | &nbsp; &nbsp; Distribuované ☐ ☐ úrovně Standard<br><br>Pokud používáte standard, není VMware HCX k dispozici. |
| DNS a DHCP<br><br>Vyžaduje se správná infrastruktura DNS a DHCP. <br>Doporučuje se používat službu DHCP integrovanou do nástroje. <br>NSX nebo použijte místní server DHCP v privátním cloudu <br>místo směrování provozu DHCP přes <br>Back-WAN zpátky do místního prostředí. Další informace <br>Podívejte [se, jak vytvořit a spravovat DHCP v řešení Azure VMware](manage-dhcp.md). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---azure-vmware-solution-infrastructure"></a>Sítě – infrastruktura řešení VMware Azure 
Potřebná data vám pomůžou splnit požadavky na používání sítě řešení Azure VMware pro účely samostatného a počátečního síťového testování. 

| Potřebné informace | Vaše odpověď |
| ----------- | ------------- |
|Azure VMware Solution CIDR<br><br>Vyžadováno pro hostitele vSphere, síti vSAN a správu <br>sítě v řešení Azure VMware. Další informace <br>informace najdete v tématu informace o [Směrování a podsíti](tutorial-network-checklist.md#routing-and-subnet-considerations). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Úlohy řešení Azure VMware pro CIDR (volitelné)<br><br>Přiřaďte síť, která se má použít ve službě Azure VMware.<br>Řešení pro počáteční testování. Virtuální počítače<br>bude nasazena za účelem ověření síťového připojení. <br>z řešení Azure VMware. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---connect-azure-vmware-solution-to-azure-virtual-network"></a>Sítě – připojení řešení Azure VMware k Azure Virtual Network
Data potřebná po stood clusteru řešení Azure VMware je možné připojit k Azure prostřednictvím okruhu ExpressRoute, který je součástí služby řešení Azure VMware.

| Potřebné informace | Vaše odpověď |
| ------------------ | ------------- |
| Rámeček přechodu – Windows Server | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Vytvoření virtuální sítě | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Vytvořit podsíť brány | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Vytvořit bránu virtuální sítě<br><br>Další informace najdete v tématu [Vytvoření brány virtuální sítě](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---connect-azure-vmware-solution-to-an-on-premises-datacenter"></a>Sítě – připojení řešení Azure VMware k místnímu datovému centru

| Potřebné informace | Vaše odpověď |
| ------------------ | ------------- |
| Partnerský vztah CIDR ExpressRoute<br><br>`/29`Blok adresy CIDR. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| ExpressRoute autorizační klíč a ID prostředku<br><br>Zadejte autorizační klíč a ID prostředku. <br>který je vygenerován z aktuálního ExpressRoute <br>okruh, který se připojuje k místnímu datovému centru.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Směr výchozí trasy<br><br>By měly virtuální počítače v řešení VMware Azure <br>přístup k Internetu prostřednictvím poskytnutého řešení Azure VMware <br>přes Internet nebo se vraťte přes okruh ExpressRoute do <br>místní pro výchozí trasu? | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Síťové porty vyžadované ke komunikaci se službou<br><br>Další informace najdete v tématu [požadované síťové porty](tutorial-network-checklist.md#required-network-ports). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---vmware-hcx"></a>Sítě – VMware HCX

| Potřebné informace | Vaše odpověď |
| ------------------ | ------------- |
| Síťové porty<br><br>Pokud je brána firewall, ujistěte se, že požadované síťové porty <br>se otevírají mezi místním prostředím a řešením Azure VMware. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| DNS<br><br>Informace o konfiguraci DNS, <br>Viz téma [síť – infrastruktura řešení VMware Azure](#networking---azure-vmware-solution-infrastructure). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| CIDRs VMware HCX<br><br>Vyžadujete dva `/29` bloky CIDR, které se používají pro <br>komponenty infrastruktury VMware HCX v místním prostředí.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |

