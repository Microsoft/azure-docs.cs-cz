---
title: Přehled služby Azure Defender a dostupných plánů
description: Přečtěte si o plánech, ochranných a výstrahách Azure Defenderu. Potom povolte Azure Defender na vašich předplatných pro pokročilé zabezpečení.
author: memildin
ms.author: memildin
ms.date: 9/30/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: bfff96666981a522cd6d91828604696a12ecad56
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91576846"
---
# <a name="introduction-to-azure-defender"></a>Seznámení s Azure Defenderem

Funkce Azure Security Center pokrývají dva široké pilíře zabezpečení cloudu:

- **CSPM (Cloud Security stav Management)** – Security Center k dispozici **zdarma** všem uživatelům Azure. Bezplatné prostředí zahrnuje CSPM funkce, jako je bezpečné skóre, detekce chybných konfigurací zabezpečení v počítačích Azure, inventáři prostředků a dalších. Pomocí těchto funkcí CSPM můžete posílit své hybridní cloudové stav a sledovat dodržování předpisů pomocí integrovaných zásad.

- **Cloudová ochrana zatížení (CWP)** Security Center – integrovaná cloudová platforma pro ochranu zatížení (CWPP), **Azure Defender**, přináší pokročilou, inteligentní a chráněnou službu Azure a hybridní prostředky a úlohy. Povolení služby Azure Defender přináší řadu dalších funkcí zabezpečení, jak je popsáno na této stránce. Kromě integrovaných zásad můžete při povolení jakéhokoli plánu Azure Defenderu přidat vlastní zásady a iniciativy. Můžete přidat zákonné standardy, jako je NIST a Azure CIS, a srovnávací test zabezpečení Azure pro skutečně přizpůsobený pohled na dodržování předpisů.

Řídicí panel Azure Defender v Security Center poskytuje viditelnost a kontrolu nad funkcemi CWP pro vaše prostředí:

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Příklad řídicího panelu Azure Defenderu" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Jaké typy prostředků můžou Azure Defender zabezpečit?

Azure Defender poskytuje výstrahy zabezpečení a pokročilou ochranu před internetovými útoky pro virtuální počítače, databáze SQL, kontejnery, webové aplikace, síť a další.

Pokud povolíte Azure Defender z oblasti **ceny a nastavení** Azure Security Center, jsou všechny tyto plány Defenderu povolené současně a poskytují komplexní ochranu pro výpočetní, datovou a provozní vrstvu vašeho prostředí:

- [Azure Defender pro servery](defender-for-servers-introduction.md)
- [Azure Defender pro App Service](defender-for-app-service-introduction.md)
- [Azure Defender pro úložiště](defender-for-storage-introduction.md)
- [Azure Defender pro SQL](defender-for-sql-introduction.md)
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

V závislosti na konkrétním prostředí budete mít přizpůsobenou analýzu hrozeb a výstrahy s upřednostněním priorit, abyste se mohli soustředit na to, co nejvíc věcí.

K rozšiřování ochrany virtuálních počítačů a databází SQL, které jsou v jiných cloudech nebo v místním prostředí, nasaďte [Azure ARC](https://azure.microsoft.com/services/azure-arc/) a povolte Azure Defender. Azure ARC pro servery je bezplatná služba, ale služby, které se používají na serverech s podporou ARC, například Azure Defender, se budou účtovat podle ceny za tuto službu. Další informace najdete v [přidávání počítačů mimo Azure pomocí ARC Azure](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc).

> [!TIP]
> Nativní konektor pro AWS transparentně zpracovává nasazení ARC Azure za vás. Další informace najdete v informacích [o připojení účtů AWS k Azure Security Center](quickstart-onboard-aws.md).



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