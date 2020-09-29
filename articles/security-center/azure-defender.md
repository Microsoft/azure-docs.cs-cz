---
title: Přehled služby Azure Defender a dostupných plánů
description: Přečtěte si o plánech, ochranných a výstrahách Azure Defenderu. Pak pokračujte v povolení Azure Defenderu na vašich předplatných.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 5a5b96d5a9ea6aa05da30238690b8f5fa745b3f3
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448432"
---
# <a name="introduction-to-azure-defender"></a>Seznámení s Azure Defenderem

Funkce Azure Security Center pokrývají dva široké pilíře zabezpečení cloudu:

- **Stav Správa cloudového zabezpečení (CSPM)**
- **Ochrana úloh v cloudu (CWP)**

CSPM Security Center funkce, jako je například zabezpečení skóre, detekce chybných konfigurací zabezpečení v počítačích Azure s Windows a Linux, jsou všechny části bezplatné Security Center dostupné všem uživatelům Azure. Pomocí těchto funkcí CSPM posílit své stav a zajistěte dodržování legislativních předpisů.

**Azure Defender** je platforma ochrany zatížení cloudu (CWPP) integrovaná v rámci Security Center pro pokročilou, inteligentní a chráněnou službu Azure a hybridní úlohy.

Toto je řídicí panel Azure Defender v Azure Security Center:

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Příklad řídicího panelu Azure Defenderu" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Jaké typy prostředků můžou Azure Defender zabezpečit?

Azure Defender poskytuje výstrahy zabezpečení a pokročilou ochranu před internetovými útoky pro virtuální počítače, databáze SQL, kontejnery, webové aplikace, síť a další.

Pokud povolíte Azure Defender z oblasti **ceny a nastavení** Azure Security Center, jsou všechny tyto plány Defenderu povolené současně a poskytují komplexní ochranu pro výpočetní, datovou a provozní vrstvu vašeho prostředí:

- [Azure Defender pro servery](defender-for-servers-introduction.md)
- [Azure Defender pro App Service](defender-for-app-service-introduction.md)
- [Azure Defender pro úložiště](defender-for-storage-introduction.md)
- [Azure Defender for SQL](defender-for-sql-introduction.md)
- [Azure Defender for IoT](defender-for-iot-introduction.md)
- [Azure Defender pro Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender pro registry kontejnerů](defender-for-container-registries-introduction.md)
- [Azure Defender pro trezor klíčů](defender-for-key-vault-introduction.md)

Každý z těchto plánů je vysvětlen samostatně v dokumentaci Security Center.


## <a name="hybrid-cloud-protection"></a>Ochrana hybridního cloudu

Stejně jako při obraně prostředí Azure můžete do svého hybridního cloudového prostředí přidat možnosti Azure Defenderu:

- Ochrana serverů mimo Azure
- Chraňte své virtuální počítače v jiných cloudech (například AWS a GCP)
- Ochrana zařízení IoT

Přizpůsobíte si přizpůsobenou analýzu hrozeb a upozornění podle konkrétního prostředí, abyste se mohli soustředit na to, co nejvíc věcí

Nasaďte [Azure ARC](https://azure.microsoft.com/services/azure-arc/) a umožněte službě Azure Defender, aby rozšířila ochranu na místní a více cloudových virtuálních počítačů a databází SQL. Azure ARC pro servery je bezplatná služba, ale služby, které se používají na serverech s podporou ARC, například Azure Defender, se budou účtovat podle ceny za tuto službu.

[Přečtěte si další informace o ARC Azure](https://docs.microsoft.com/azure/azure-arc/overview).


## <a name="azure-defender-alerts"></a>Upozornění Azure Defenderu 

Když Azure Defender zjistí hrozbu v jakékoli oblasti vašeho prostředí, vygeneruje výstrahu. Tyto výstrahy popisují podrobnosti o ovlivněných prostředcích, navrhovaných krocích pro nápravu a v některých případech možnost aktivovat aplikaci logiky v reakci.

Bez ohledu na to, jestli je výstraha vygenerovaná Security Center nebo přijatá Security Center z integrovaného bezpečnostního produktu, můžete ji exportovat. Pokud chcete exportovat výstrahy do Azure Sentinel, všech SIEM třetích stran nebo jakýchkoli jiných externích nástrojů, postupujte podle pokynů v tématu [Export výstrah do Siem](continuous-export.md).

> [!NOTE]
> Zobrazení výstrah z různých zdrojů může trvat různě dlouho. Například výstrahy, které vyžadují analýzu síťového provozu, mohou trvat déle než výstrahy související s podezřelými procesy běžícími na virtuálních počítačích.


## <a name="azure-defender-advanced-protection-capabilities"></a>Možnosti rozšířené ochrany v programu Azure Defender

Azure Defender používá pokročilou analýzu pro přizpůsobená doporučení týkající se vašich prostředků. 

Ochrana zahrnuje zabezpečení portů pro správu virtuálních počítačů pomocí přístupu za běhu a adaptivních řízení aplikací, které umožňuje vytvářet seznamy povolených aplikací, které by měly a neměly běžet na vašich počítačích. 

Pomocí dlaždic rozšířené ochrany v řídicím panelu Azure Defender můžete monitorovat a konfigurovat jednotlivé tyto ochrany. 

## <a name="vulnerability-assessment-and-management"></a>Posouzení ohrožení zabezpečení a Správa

Azure Defender zahrnuje kontrolu ohrožení zabezpečení pro vaše virtuální počítače a Registry kontejnerů bez dalších poplatků. Skenery využívají Qualys, ale nepotřebujete licenci Qualys ani dokonce účet Qualys – všechno se bez problémů zpracovává v Security Center. 

Projděte si závěry z těchto skenerů ohrožení zabezpečení a odpovězte na ně z Security Center. To přináší Security Center blíž k jednomu podoknu skla pro všechny vaše úsilí cloudového zabezpečení.

Další informace najdete na následujících stránkách:

- [Integrované řešení posouzení ohrožení zabezpečení Security Center pro virtuální počítače Azure](deploy-vulnerability-assessment-vm.md)
- [Identifikace ohrožení zabezpečení v obrázcích ve službě Azure Container Registry](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o výhodách Azure Defenderu. 

> [!div class="nextstepaction"]
> [Povolit Azure Defender](security-center-pricing.md)