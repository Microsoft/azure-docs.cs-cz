---
title: Fakturace účtovaná podle objemu dat pomocí služby měření tržiště | Azure Marketplace
description: Tato dokumentace je příručkou pro nesežené firmy, kteří publikují nabídky SaaS s flexibilními fakturačními modely.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 8e5a4813301cbab16d1cffabaaa60688f6e826ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281319"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>Fakturace účtované podle objemu dat pomocí služby měření tržiště

Pomocí služby měření Marketplace můžete vytvářet nabídky softwaru jako služby (SaaS) v komerčním programu marketplace, které se účtují podle nestandardních jednotek.  Před publikováním této nabídky definujete fakturační dimenze, jako je šířka pásma, lístky nebo zpracované e-maily.  Zákazníci pak platí podle spotřeby těchto dimenzí, přičemž váš systém informuje společnost Microsoft prostřednictvím rozhraní API služby měření Marketplace o fakturovatelných událostech, jakmile k nim dojde.  

## <a name="prerequisites-for-metered-billing"></a>Požadavky pro účtování účtované podle objemu dat

Aby nabídka SaaS mohla používat fakturaci účtované podle objemu dat, musí:

* Splňte všechny požadavky na nabídku [pro prodej prostřednictvím nabídky microsoftu,](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft) jak je uvedeno v [nabídce Vytvořit SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer).
* Integrujte s [api s plněním SaaS,](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) aby zákazníci zjidní a připojí se k vaší nabídce.  
* Nakonfigurujte pro model **paušálních** cen pro účtování poplatků zákazníkům za vaši službu.  Dimenze jsou volitelným rozšířením modelu paušálních cen. 
* Integrujte s [rozhraními API služby měření Marketplace a](./marketplace-metering-service-apis.md) informujte společnost Microsoft o fakturovatelných událostech.

>[!Note]
>Služba měření marketplace je k dispozici pouze pro model paušální fakturace a nevztahuje se na model fakturace pro jednotlivé uživatele.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Jak se odměřená fakturace hodí k cenám

Pokud jde o definování nabídky spolu s jejími cenovými modely, je důležité pochopit hierarchii nabídek.

* Každá nabídka SaaS je nakonfigurována tak, aby se prodávala buď prostřednictvím společnosti Microsoft, nebo ne.  Toto nastavení nelze po publikování nabídky změnit.
* Každá nabídka SaaS, nakonfigurovaná pro prodej prostřednictvím microsoftu, může mít jeden nebo více plánů. Uživatel se přihlásí k odběru nabídky SaaS, ale je zakoupenprostřednictvím společnosti Microsoft v rámci plánu.
* Ke každému plánu je přidružen cenový model: **paušální sazba** nebo **na uživatele**. Všechny plány v nabídce musí být přidruženy ke stejnému cenovému modelu. Například nemůže existovat nabídka, kde jeden z jeho plánů je paušální cenový model a jiný je pro uživatele cenový model.
* V rámci každého plánu nakonfigurovaného pro model paušální fakturace je zahrnut alespoň jeden opakovaný poplatek (který může být $0):
    * Opakující se **měsíční** poplatek: paušální měsíční poplatek, který je předplacen při měsíčním opakování, když uživatel zakoupí plán.
    * Opakující se **roční** poplatek: paušální roční poplatek, který je předplacen při ročním opakování, když uživatel zakoupí plán.
* Kromě opakujících se poplatků může plán obsahovat také volitelné dimenze používané k fakturaci zákazníkům za využití, které není zahrnuto ve paušální sazbě.   Každá dimenze představuje fakturovatelnou jednotku, kterou bude vaše služba komunikovat se společností Microsoft pomocí [rozhraní API služby měření Marketplace](./marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Ukázková nabídka

Jako příklad je společnost Contoso vydavatelem se službou SaaS nazvanou Contoso Notification Services (CNS). CNS umožňuje zákazníkům posílat oznámení buď e-mailem nebo textovou zprávou. Contoso je registrovaný jako vydavatel v Partnerském centru pro komerční tržiště program publikovat nabídky pro zákazníky Azure.  Existují dva plány spojené s CNS, uvedené níže:

* Základní plán
    * Pošlete 10000 e-mailů a 1000 textů za $ 0 / měsíc
    * Kromě e-mailů 10000 zaplaťte 1 dolar za každých 100 e-mailů
    * Kromě 1000 textů, zaplatit 0,02 dolarů za každý text
* Plán Premium
    * Pošlete 50000 e-mailů a 10000 textů za $ 350 / měsíc
    * Kromě e-mailů 50000 zaplaťte 0,5 USD za každých 100 e-mailů
    * Kromě 10000 textů, zaplatit 0,01 dolarů za každý text

Zákazník Azure, který se přihlásí ke službě CNS, bude moct odesílat zahrnuté množství textu a e-mailů za měsíc na základě vybraného plánu.  Společnost Contoso měří využití až do zahrnutého množství bez odeslání událostí využití společnosti Microsoft.  Když zákazníci spotřebovávají více než zahrnuté množství, nemusí měnit plány ani dělat nic jiného.  Společnost Contoso bude měřit nadbytek nad rámec zahrnutého množství a začne vypouštět události využití společnosti Microsoft pro další využití pomocí [rozhraní API služby měření Marketplace](./marketplace-metering-service-apis.md).  Společnost Microsoft bude zákazníkovi účtovat další využití určené vydavatelem.

## <a name="billing-dimensions"></a>Fakturační dimenze

Fakturační dimenze se používají ke sdělení zákazníkovi o tom, jak se bude účtovat za používání softwaru, a také ke sdělování událostí využití společnosti Microsoft. Jsou definovány takto:

* **Identifikátor dimenze**: neměnný identifikátor odkazovaný při vyzařování událostí použití.
* **Název dimenze**: zobrazovaný název přidružený k dimenzi, např.
* **Měrná jednotka:** popis fakturační jednotky, např.
* **Cena za jednotku**: cena za jednu jednotku dimenze.  
* **Zahrnuté množství pro měsíční období:** množství dimenze zahrnuté za měsíc pro zákazníky platící opakující se měsíční poplatek musí být celé číslo.
* **Zahrnuté množství pro roční období:** množství dimenze zahrnuté za měsíc pro zákazníky platící opakující se roční poplatek musí být celé číslo.

Fakturační dimenze jsou sdíleny ve všech plánech pro nabídku.  Některé atributy platí pro dimenzi ve všech plánech a jiné atributy jsou specifické pro plán.

Atributy, které definují samotnou dimenzi, jsou sdíleny ve všech plánech nabídky.  Před publikováním nabídky ovlivní změna těchto atributů z kontextu libovolného plánu definici dimenze ve všech plánech.  Po publikování nabídky již nebudou tyto atributy upravovat.  Jsou to tyto atributy:

* Identifikátor
* Name (Název)
* Jednotka měření

Ostatní atributy dimenze jsou specifické pro každý plán a mohou mít různé hodnoty z plánu do plánu.  Před publikováním plánu můžete tyto hodnoty upravit a ovlivní pouze tento plán.  Po publikování plánu již nebudou tyto atributy upravovat.  Jsou to tyto atributy:

* Cena za jednotku
* Zahrnuté množství pro měsíční zákazníky 
* Zahrnuté množství pro roční odběratele 

Rozměry mají také dva speciální koncepty, "povoleno" a "nekonečné":

* **Povoleno** označuje, že tento plán se účastní této dimenze.  Tuto nezaškrtnutou můžete ponechat, pokud vytváříte nový plán, který neodesílá události využití na základě této dimenze.  Všechny nové dimenze přidané po prvním publikování plánu se také zobrazí jako "není povoleno" v již publikovaném plánu.  Zakázaná dimenze se nezobrazí v žádném seznamu dimenzí pro plán, který uvidí zákazníci.
* **Infinite**, reprezentované symbolem nekonečna "∞", označuje, že tento plán se účastní této dimenze, ale není metr použití proti této dimenzi.  Pokud chcete zákazníkům sdělovat, že funkce reprezentované touto dimenzí jsou zahrnuty v plánu, ale bez omezení využití.  Dimenze s nekonečným využitím se zobrazí v seznamech dimenzí pro plán, který uvidí zákazníci, s uvedením, že za tento plán nikdy nebude účtován poplatek.

>[!Note] 
>Následující scénáře jsou explicitně podporovány: <br> - Do nového plánu můžete přidat novou dimenzi.  Nová dimenze nebude povolena pro všechny již publikované plány. <br> - Můžete publikovat **plán s paušální sazbou** bez jakýchkoli dimenzí, pak přidat nový plán a nakonfigurovat novou dimenzi pro tento plán. Nová dimenze nebude povolena pro již publikované plány.

## <a name="constraints"></a>Omezení

### <a name="trial-behavior"></a>Zkušební chování

Fakturace účtovaná podle objemu dat pomocí služby měření tržiště není kompatibilní s nabídkou bezplatné zkušební verze.  Není možné nakonfigurovat plán pro použití fakturace účtované podle objemu dat i bezplatné zkušební verze.

### <a name="locking-behavior"></a>Chování uzamčení

Vzhledem k tomu, že dimenze používaná se službou měření Marketplace představuje pochopení toho, jak bude zákazník platit za službu, všechny podrobnosti o dimenzi již nelze upravovat, jakmile ji publikujete.  Je důležité, abyste měli své dimenze plně definované pro plán před publikováním.
  
Jakmile je nabídka publikována s dimenzí, podrobnosti o úrovni nabídky pro tuto dimenzi již nelze změnit:

* Identifikátor
* Name (Název)
* Jednotka měření

Po publikování plánu již nelze změnit podrobnosti na úrovni plánu:

* Cena za jednotku
* Zahrnuté množství pro měsíční období
* Zahrnuté množství pro roční období
* Zda je dimenze povolena pro plán

### <a name="upper-limits"></a>Horní hranice

Maximální počet dimenzí, které lze nakonfigurovat pro jednu nabídku, je 18 jedinečných dimenzí.

## <a name="get-support"></a>Získat podporu

Pokud máte jednu z následujících možností, můžete otevřít lístek podpory.

* Technické problémy s rozhraním API služby měření marketplace.
* Problém, který je třeba eskalovat z důvodu chyby nebo chyby na vaší straně (např. nesprávný způsob použití).
* Jakékoli další problémy související s fakturací účtovaným podle objemu dat 

Podle následujících kroků odešlete lístek podpory:

1. Přejděte na [stránku podpory](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff). Prvních několik rozbalovacích nabídek se automaticky vyplní za vás. Pro podporu Marketplace identifikujte produktovou skupinu jako **Clouda a Online služby**, produkt jako **Marketplace Publisher**.  Neměňte předem vyplněný výběr rozbalovací nabídky.
2. V části "Vyberte verzi produktu" vyberte **Live offer management**.
3. V části "Vyberte kategorii, která nejlépe popisuje problém", zvolte **aplikace SaaS**.
4. V části Vyberte problém, který nejlépe popisuje problém, vyberte **fakturaci účtované podle objemu dat**.
5. Výběrem tlačítka **Další** budete přesměrováni na stránku **Podrobnosti o problému,** kde můžete zadat další podrobnosti o problému.

Další možnosti podpory vydavatelů najdete [v tématu Podpora komerčního programu Marketplace v Centru partnerů.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support)

## <a name="next-steps"></a>Další kroky

- Další informace najdete [v tématu Marketplace metering service API.](./marketplace-metering-service-apis.md)
