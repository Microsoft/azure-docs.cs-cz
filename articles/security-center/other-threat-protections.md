---
title: Další ochrana před hrozbami v Azure Security Center
description: Přečtěte si o ochraně před hrozbami dostupnými Azure Security Center mimo Azure Defender.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: overview
ms.date: 09/15/2020
ms.author: memildin
ms.openlocfilehash: 63b93db314701d281f3f4fff195671f43cdb9dbc
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340882"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>Další ochrana před hrozbami v Azure Security Center
I integrované [ochrany v Azure defenderu](azure-defender.md)Azure Security Center taky nabízí následující možnosti ochrany před hrozbami.

> [!TIP]
> Pokud chcete povolit možnosti ochrany před hrozbami Security Center, musíte v předplatném, které obsahuje příslušné úlohy, povolit Azure Defender.
>
> Ochranu před hrozbami pro **Azure Database for MariaDB/MySQL/PostgreSQL** můžete povolit jenom na úrovni prostředků.


## <a name="threat-protection-for-azure-network-layer"></a>Ochrana před hrozbami pro síťovou vrstvu Azure <a name="network-layer"></a>
Analýza Security Centerch síťových vrstev vychází z ukázkových [dat IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), což jsou hlavičky paketů shromážděné směrovači Azure Core. Na základě tohoto datového kanálu používá Security Center k identifikaci a označení aktivit škodlivého provozu modely strojového učení. Security Center k rozšíření IP adres používá taky databázi Microsoft Threat Intelligence.

Některé konfigurace sítě mohou omezit Security Center generování výstrah na podezřelé síťové aktivity. Pokud Security Center chcete generovat výstrahy sítě, zajistěte, aby:
- Váš virtuální počítač má veřejnou IP adresu (nebo se nachází ve službě Vyrovnávání zatížení s veřejnou IP adresou).
- Externí řešení identifikátorů ID neblokuje provoz odchozího výstupu sítě vašeho virtuálního počítače.
- Vašemu virtuálnímu počítači se přiřadila stejná IP adresa pro celou hodinu, během které došlo k podezřelé komunikaci. To platí také pro virtuální počítače vytvořené jako součást spravované služby (například AKS, datacihly).

Seznam výstrah síťových vrstev Azure najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-azurenetlayer).


## <a name="threat-protection-for-azure-resource-manager-preview"></a>Ochrana před hrozbami pro Azure Resource Manager (Preview)<a name ="management-layer"></a>
Vrstva ochrany Security Center založená na Azure Resource Manager je momentálně ve verzi Preview.

Security Center nabízí další vrstvu ochrany pomocí Azure Resource Managerch událostí, které se považují za řídicí plochu pro Azure. Díky analýze záznamů Azure Resource Manager Security Center detekuje neobvyklé nebo potenciálně škodlivé operace v prostředí předplatného Azure.

Seznam výstrah Azure Resource Manager (Preview) najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-azureresourceman).


>[!NOTE]
> Některé z předchozích analýz jsou napájené z Microsoft Cloud App Security. Pokud chcete tyto analýzy využít, musíte aktivovat licenci Cloud App Security. Pokud máte licenci Cloud App Security, jsou tyto výstrahy ve výchozím nastavení povolené. Zakázání výstrah:
>
> 1. V nabídce Security Center vyberte **cenové & nastavení**.
> 1. Vyberte předplatné, které chcete změnit.
> 1. Vyberte **detekci hrozeb**.
> 1. Zrušte zaškrtnutí políčka **Allow Microsoft Cloud App Security pro přístup k datům**a vyberte **Uložit**.


>[!NOTE]
>Security Center ukládá zákaznická data týkající se zabezpečení ve stejné geografické podobě jako její prostředek. Pokud společnost Microsoft ještě nebyla nasazena Security Center v geografickém prostředku, uloží data do USA. Pokud je povolená Cloud App Security, ukládají se tyto informace v souladu s pravidly geografického umístění Cloud App Security. Další informace najdete v tématu [úložiště dat pro jiné než regionální služby](https://azuredatacentermap.azurewebsites.net/).

1. Nastavte pracovní prostor, do kterého instalujete agenta. Ujistěte se, že je pracovní prostor ve stejném předplatném, které používáte v Security Center a máte oprávnění ke čtení a zápisu v pracovním prostoru.

1. Povolte **Azure Defender**a vyberte **Uložit**.


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Ochrana před hrozbami pro Azure Cosmos DB (Preview)<a name="cosmos-db"></a>

Výstrahy Azure Cosmos DB jsou generovány neobvyklými a potenciálně škodlivými pokusy o přístup k účtům Azure Cosmos DB nebo jejich zneužití.

Další informace naleznete v tématech:

* [Rozšířená ochrana před internetovými útoky pro Azure Cosmos DB (Preview)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Seznam výstrah ochrany před hrozbami pro Azure Cosmos DB (Preview)](alerts-reference.md#alerts-azurecosmos)



## <a name="threat-protection-for-other-microsoft-services"></a>Ochrana před hrozbami pro další služby Microsoftu <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Ochrana před hrozbami pro Azure WAF <a name="azure-waf"></a>

Azure Application Gateway nabízí Firewall webových aplikací (WAF) poskytující centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení.

Webové aplikace jsou stále více zaměřené na škodlivé útoky, které využívají často známá ohrožení zabezpečení. Application Gateway WAF vychází ze základní sady pravidel 3,0 nebo 2.2.9 v otevřeném projektu zabezpečení webové aplikace. WAF se automaticky aktualizuje, aby se chránila před novými chybami zabezpečení. 

Pokud máte licenci pro Azure WAF, vaše výstrahy WAF se streamují Security Center bez nutnosti další konfigurace. Další informace o výstrahách vygenerovaných službou WAF najdete v tématu [skupiny pravidel a pravidla pro Firewall webových aplikací](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Ochrana před hrozbami pro Azure DDoS Protection <a name="azure-ddos"></a>

Je známo, že je možné snadno spustit útok s cílem odepření služeb (DDoS). Jsou to skvělé problémy se zabezpečením, zejména pokud přesouváte aplikace do cloudu. 

Útok DDoS se pokusí vyčerpat prostředky aplikace, takže aplikace nebude k dispozici oprávněným uživatelům. Útoky DDoS můžou cílit na libovolný koncový bod, ke kterému se dá získat přístup přes Internet.

Pokud chcete chránit před útoky DDoS, Zakupte si licenci pro Azure DDoS Protection a ujistěte se, že jste si jisti osvědčenými postupy pro návrh aplikace. DDoS Protection poskytuje různé úrovně služeb. Další informace najdete v tématu [přehled Azure DDoS Protection](../virtual-network/ddos-protection-overview.md).

Seznam výstrah Azure DDoS Protection najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Další kroky
Další informace o výstrahách zabezpečení z těchto funkcí ochrany před hrozbami najdete v následujících článcích:

* [Referenční tabulka pro všechny výstrahy Azure Security Center](alerts-reference.md)
* [Výstrahy zabezpečení ve službě Azure Security Center](security-center-alerts-overview.md)
* [Správa a reakce na upozornění zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Exportovat výstrahy a doporučení zabezpečení (Preview)](continuous-export.md)