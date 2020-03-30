---
title: Automatizace úloh pro podnikovou integraci
description: Seznamte se s automatizací pracovních postupů, které integrují aplikace, data, služby a systémy s minimálním kódem pro podnikovou integraci pomocí Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: overview
ms.custom: mvc
ms.date: 03/11/2020
ms.openlocfilehash: 0ba41d63195c906b57046dc6c9fd57c9f08399ab
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241665"
---
# <a name="overview---what-is-azure-logic-apps"></a>Přehled – Co jsou aplikace Azure Logic Apps?

[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps) je cloudová služba, která vám pomůže plánovat, automatizovat a organizovat [úlohy,](#logic-app-concepts) obchodní procesy a pracovní postupy, když potřebujete integrovat aplikace, data, systémy a služby napříč podniky nebo organizacemi. Logic Apps zjednodušuje návrh a vytváření škálovatelných řešení pro [integraci](https://azure.microsoft.com/product-categories/integration/)aplikací , integraci dat, integraci systému, integraci podnikových aplikací (EAI) a komunikaci mezi podniky (B2B), ať už v cloudu, místně nebo obojí.

Tady je například pouze několik úloh, které můžete pomocí aplikací logiky automatizovat:

* Objednávky procesů a tras napříč místními systémy a cloudovými službami.

* Odesílání e-mailových oznámení pomocí Office 365 v reakci na události v různých systémech, aplikacích a službách.

* Přesun nahraných souborů ze serveru SFTP nebo FTP do služby Azure Storage.

* Monitorování výskytu konkrétního tématu ve tweetech, analýza mínění a vytváření upozornění na položky, které vyžadují kontrolu.

Chcete-li vytvářet řešení pro podnikovou integraci pomocí aplikací Azure Logic Apps, můžete si vybrat z rostoucí galerie se [stovkami konektorů připravených k použití](../connectors/apis-list.md), které zahrnují služby jako Azure Service Bus, Azure Functions, Azure Storage, SQL Server, Office 365, Dynamics, Salesforce, BizTalk, SAP, Oracle DB, sdílení souborů a další. [Konektory](#logic-app-concepts) poskytují [triggery](#logic-app-concepts), [akce](#logic-app-concepts) nebo obojí pro vytváření aplikací logiky, které bezpečně přistupují k datům a zpracovávají je v reálném čase.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Introducing-Azure-Logic-Apps/player]

## <a name="how-does-logic-apps-work"></a>Jako Logic Apps funguje? 

Každý pracovní postup aplikace logiky se spouští triggerem, který se aktivuje při určité události nebo když nová dostupná data splní určitá kritéria. Mnoho aktivačních událostí poskytovaných konektory v logic apps zahrnují základní možnosti plánování, takže můžete nastavit, jak pravidelně vaše úlohy spustit. Pro složitější plánování nebo upřesňující opakování můžete použít aktivační událost opakování jako první krok v libovolném pracovním postupu. Přečtěte si další informace o [pracovních postupech založených na plánu](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Pokaždé, když se trigger aktivuje, vytvoří modul Logic Apps instanci aplikace logiky, ve které se spustí akce v pracovním postupu. Tyto akce mohou také zahrnovat převody dat a ovládací prvky pracovního postupu, jako jsou podmíněné příkazy, příkazy přepínačů, smyčky a větvení. Například tato aplikace logiky se spouští triggerem Dynamics 365 s integrovaným kritériem Při aktualizaci záznamu. Pokud trigger rozpozná událost, která splňuje toto kritérium, aktivuje se a spustí akce pracovního postupu. V tomto případě mezi tyto akce patří transformace XML, aktualizace dat, větvení rozhodování a e-mailová oznámení.

![Návrhář pro Logic Apps – příklad aplikace logiky](./media/logic-apps-overview/azure-logic-apps-designer.png)

Aplikace logiky můžete vytvářet vizuálně pomocí Návrháře pro Logic Apps, který je k dispozici v prohlížeči na webu Azure Portal a v sadě Visual Studio. V případě dalších vlastních aplikací logiky můžete vytvářet nebo upravovat definice aplikací logiky ve formátu JSON (JavaScript Object Notation) při práci v editoru v režimu Zobrazení kódu. Pro vybrané úlohy můžete použít také příkazy Azure PowerShellu a šablony Azure Resource Manageru. Aplikace logiky se nasazují a spouštějí v cloudu Azure. Podrobnější informace najdete v tomto videu: [Použití služeb Azure pro podnikovou integraci k provozu cloudových aplikací ve velkém měřítku](https://channel9.msdn.com/Events/Connect/2017/T119/).

## <a name="why-use-logic-apps"></a>Proč používat Azure Logic Apps?

S tím, jak se firmy přesouvají směrem k digitalizaci, pomáhají aplikace logiky snadněji a rychleji propojovat starší, moderní i ty nejmodernější systémy díky tomu, že poskytují předem připravená rozhraní API v podobě konektorů spravovaných Microsoftem. Díky tomu se můžete zaměřit na obchodní logiku a funkce vašich aplikací. Nemusíte se starat o vytváření, hostování, škálování, správu, údržbu ani monitorování svých aplikací. Logic Apps se o to postará za vás. Navíc platíte jenom za to, co využijete, podle [cenového modelu](../logic-apps/logic-apps-pricing.md) založeného na spotřebě.

V řadě případů nebudete muset psát žádný kód. Pokud však nějaký kód napsat musíte, pomocí [Azure Functions](../azure-functions/functions-overview.md) můžete vytvářet fragmenty kódu a v aplikacích logiky spouštět tento kód na vyžádání. Navíc pokud vaše aplikace logiky potřebují interagovat s událostmi ze služeb Azure, vlastních aplikací nebo jiných řešení, můžete ve svých aplikacích logiky použít [Azure Event Grid](../event-grid/overview.md) zajišťující monitorování, směrování a publikování událostí.

Logic Apps, Functions a Event Grid jsou plně spravované Microsoft Azure, takže nemusíte mít obavy o vytváření, hostování, škálování, správu, monitorování ani údržbu svých řešení. Díky možnosti vytvářet [aplikace a řešení bez serveru](../logic-apps/logic-apps-serverless-overview.md) se můžete zaměřit pouze na obchodní logiku. Tyto služby se automaticky škálují s ohledem na vaše požadavky, zrychlují integrace a pomáhají vytvářet robustní cloudové aplikace s minimem kódu.

Pokud chcete zjistit, jak společnosti díky kombinaci Logic Apps s dalšími službami Azure a produkty Microsoftu zlepšily svou flexibilitu a zvýšili zaměření na hlavní obchodní aktivity, přečtěte si tyto [příběhy zákazníků](https://aka.ms/logic-apps-customer-stories).

Tady jsou další podrobnosti o schopnostech a výhodách, které vám Logic Apps přinese:

### <a name="visually-build-workflows-with-easy-to-use-tools"></a>Vizuální vytváření pracovních postupů pomocí snadno použitelných nástrojů

Šetřete čas a zjednodušte složité procesy pomocí nástrojů pro vizuální návrh. Vytvářejte aplikace logiky od začátku do konce pomocí Návrháře pro Logic Apps, a to v prohlížeči na webu Azure Portal nebo v sadě Visual Studio. Spouštějte pracovní postupy pomocí triggerů a přidejte jakýkoli počet akcí z [galerie konektorů](../connectors/apis-list.md).

### <a name="get-started-faster-with-logic-app-templates"></a>Rychlejší začátek díky šablonám aplikací logiky

Vytvářejte běžně používaná řešení rychleji díky možnosti výběru předdefinovaných pracovních postupů z [galerie šablon](../logic-apps/logic-apps-create-logic-apps-from-templates.md). Šablony jsou v rozsahu od jednoduché možnosti připojení pro aplikace typu software jako služba (SaaS) až po pokročilá řešení B2B a šablony jen pro zábavu. Zjistěte, jak [vytvářet aplikace logiky z předem připravených šablon](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

### <a name="connect-disparate-systems-across-different-environments"></a>Propojení různorodých systémů napříč různými prostředími

Některé vzory a pracovní postupy je snadné popsat, ale složité implementovat v kódu. Aplikace logiky pomáhají bezproblémově propojit různorodé systémy napříč místním a cloudovým prostředím. Můžete například propojit cloudové marketingové řešení s místním fakturačním systémem nebo pomocí služby Service Bus úrovně Enterprise centralizovat zasílání zpráv napříč rozhraními API a systémy. Aplikace logiky poskytují rychlý, spolehlivý a konzistentní způsob doručování znovupoužitelných a znovu konfigurovatelných řešení pro tyto scénáře.

### <a name="first-class-support-for-enterprise-integration-and-b2b-scenarios"></a>Prvotřídní podpora podnikové integrace a scénářů B2B

Podniky a organizace mezi sebou elektronicky komunikují s využitím standardních, ale rozdílných protokolů a formátů zpráv, jako jsou EDIFACT, AS2 a X12. S využitím funkcí v sadě [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md) můžete vytvářet aplikace logiky transformující formáty zpráv, které používají vaši partneři, na formáty, které dokážou interpretovat a zpracovat systémy vaší organizace. Logic Apps tyto výměny zpracovává plynule a zabezpečeně díky šifrování a digitálním podpisům.

Začněte v malém se svými stávajícími systémy a službami a rozšiřujte se vlastním tempem. Jakmile budete připraveni, Logic Apps a EIP vám díky zajištění nejen těchto schopností pomůžou implementovat a vertikálně navýšit kapacitu na vyspělejší scénáře integrace:

* Sestavení z těchto produktů a služeb:

  * [Microsoft BizTalk Server](https://docs.microsoft.com/biztalk/core/introducing-biztalk-server)
  * [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)
  * [Azure Functions](../azure-functions/functions-overview.md)
  * [Azure API Management](../api-management/api-management-key-concepts.md)

* Zpracování [zpráv XML](../logic-apps/logic-apps-enterprise-integration-xml.md)

* Zpracování [plochých souborů](../logic-apps/logic-apps-enterprise-integration-flatfile.md)

* Výměna zpráv s využitím protokolů [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md), [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md) a [X12](../logic-apps/logic-apps-enterprise-integration-x12.md)

* Ukládání a správa těchto artefaktů B2B a dalších na jednom místě díky [účtům integrace](../logic-apps/logic-apps-enterprise-integration-accounts.md):

  * [Partneři](../logic-apps/logic-apps-enterprise-integration-partners.md)
  * [Smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md) 
  * [Mapy transformace XML](../logic-apps/logic-apps-enterprise-integration-maps.md)
  * [Schémata ověření XML](../logic-apps/logic-apps-enterprise-integration-schemas.md)

Pokud například používáte Microsoft BizTalk Server, můžou s ním aplikace logiky komunikovat pomocí [konektoru BizTalk Server](../connectors/apis-list.md#on-premises-connectors). Do aplikací logiky pak můžete zahrnout [konektory účtu integrace](../connectors/apis-list.md#integration-account-connectors), které jsou součástí sady Enterprise Integration Pack, a rozšířit nebo provádět operace jako BizTalk.

A naopak, BizTalk Server se může připojit k aplikacím logiky a komunikovat s nimi pomocí [adaptéru Microsoft BizTalk Serveru pro Logic Apps](https://www.microsoft.com/download/details.aspx?id=54287). Zjistěte, jak na svém BizTalk Serveru [nastavit a používat adaptér BizTalk Serveru](https://docs.microsoft.com/biztalk/core/logic-app-adapter).

### <a name="write-once-reuse-often"></a>Časté používání jednou napsaného kódu

Vytvořte aplikace logiky jako šablony Azure Resource Manageru, abyste mohli [automatizovat nasazení aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) ve více prostředích a oblastech.

### <a name="access-resources-inside-azure-virtual-networks"></a>Přístup k prostředkům ve virtuálních sítích Azure

Aplikace logiky přístup k zabezpečené prostředky, jako jsou virtuální počítače (VM) a další systémy nebo služby, které jsou uvnitř [virtuální sítě Azure](../virtual-network/virtual-networks-overview.md) při vytváření prostředí [ *služby integrace* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). ISE je izolovaná instance služby Logic Apps, která používá vyhrazené prostředky a běží odděleně od "globální" služby Logic Apps pro více klientů.

Spuštění aplikací logiky ve vlastní samostatné izolované instanci pomáhá snížit dopad, který mohou mít ostatní klienti Azure na výkon vašich aplikací, označovaný také jako [efekt "hlučné sousedy".](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) ISE také poskytuje tyto výhody:

* Vlastní statické IP adresy, které jsou oddělené od statických IP adres, které jsou sdíleny aplikacemi logiky ve službě s více klienty. Můžete také nastavit jednu veřejnou, statickou a předvídatelnou odchozí ADRESU IP pro komunikaci s cílovými systémy. Tímto způsobem není třeba nastavit další otvory brány firewall v těchto cílových systémech pro každou ISE.

* Zvýšená omezení doby běhu, uchovávání úložiště, propustnost, časové limity požadavků http a odpovědí, velikosti zpráv a vlastní požadavky konektoru. Další informace najdete v [tématu Limity a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md).

Když vytvoříte ISE, Azure *vloží* nebo nasadí tuto službu ISE do virtuální sítě Azure. Tuto službu ISE pak můžete použít jako umístění pro aplikace logiky a účty integrace, které potřebují přístup. Další informace o vytváření služby ISE najdete v tématu [Připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

### <a name="built-in-extensibility"></a>Integrovaná rozšiřitelnost

Pokud nenajdete konektor, který chcete spustit vlastní kód, můžete rozšířit aplikace logiky vytvořením a voláním vlastních fragmentů kódu na vyžádání prostřednictvím [funkce Azure](../azure-functions/functions-overview.md). Vytvářejte vlastní rozhraní [API](../logic-apps/logic-apps-create-api-app.md) a [vlastní konektory](../logic-apps/custom-connector-overview.md), které můžete volat z aplikací logiky.

### <a name="pay-only-for-what-you-use"></a>Platíte jenom za to, co využijete
  
Pokud nemáte aplikace logiky vytvořené dříve s plány služby App Service, Logic Apps používá [ceny a měření](../logic-apps/logic-apps-pricing.md) založené na spotřebě.

Další informace o Logic Apps najdete v těchto úvodních videích:

* [Integrace s Logic Apps – Ze začátečníka profesionálem](https://channel9.msdn.com/Events/Build/2017/C9R17)
* [Podniková integrace s Microsoft Azure Logic Apps](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2188)
* [Vytváření pokročilých obchodních procesů pomocí Logic Apps](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3179)

## <a name="how-does-logic-apps-differ-from-functions-webjobs-and-power-automate"></a>Jak se logicistky liší od functions, WebJobs a Power Automate?

Všechny tyto služby pomáhají spojit a vzájemně propojit různorodé systémy. Každá služba má své výhody, takže nejlepším způsobem, jak rychle vytvořit škálovatelný a plně vybavený systém integrace, je kombinace jejich schopnosti. Další informace najdete v [tématu Výběr mezi logicem aplikace, funkce, webová úloha a power automate](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

<a name="logic-app-concepts"></a>

## <a name="key-terms"></a>Klíčové pojmy

* **Pracovní postup:** Vizualizace, návrh, tvorba, automatizace a nasazování obchodních procesů jako série kroků.

* **Spravované konektory:** Aplikace logiky potřebují přístup k datům, službám a systémům. Můžete použít předem připravené konektory spravované Microsoftem, které jsou navržení pro připojení, přístup a práci s vašimi daty. Viz [Konektory pro aplikace Logika Azure](../connectors/apis-list.md).

* **Triggery:** Řada konektorů spravovaných Microsoftem poskytuje triggery, které se aktivují, když událost nebo nová data splní zadané podmínky. Událostí může být například přijetí e-mailu nebo detekce změn v účtu služby Azure Storage. Pokaždé, když se trigger aktivuje, vytvoří modul Logic Apps novou instanci aplikace logiky, ve které se spustí pracovní postup.

* **Akce:** Akce jsou všechny kroky, které se stanou po aktivaci triggeru. Každá akce se obvykle mapuje na operaci definovanou spravovaným konektorem, vlastním rozhraním API nebo vlastním konektorem.

* **Enterprise Integration Pack:** Logic Apps zahrnuje možnosti z BizTalk Serveru a nabízí tak pokročilejší scénáře integrace. Enterprise Integration Pack poskytuje konektory, které aplikacím logiky pomáhají snadno provádět ověřování, transformaci a další operace.

## <a name="get-started"></a>Začínáme

Logic Apps je jednou z mnoha služeb hostovaných v Microsoft Azure. Takže než začnete, potřebujete předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

Pokud máte předplatné Azure, vyzkoušejte tento [rychlý start k vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md), která prostřednictvím informačního kanálu RSS monitoruje nový obsah na webu a odesílá e-mail, když se objeví nový obsah.

## <a name="next-steps"></a>Další kroky

* [Kontrola provozu s využitím aplikace logiky založené na plánu](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
* Další informace o [řešeních bez serveru s využitím Azure](../logic-apps/logic-apps-serverless-overview.md)
* Další informace o [integraci B2B pomocí sady Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
