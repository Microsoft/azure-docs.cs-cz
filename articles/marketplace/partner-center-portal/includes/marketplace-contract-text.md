---
title: zahrnout soubor
description: zahrnutí souboru textu do smlouvy Microsoft Commercial Marketplace Standard
documentationcenter: partner-center-commercial-marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 01/22/2020
ms.custom: include file
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: e1119cf5c40cdcb1fd598b85410ea9149a7cc146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400946"
---
Microsoft poskytuje standardní šablonu smlouvy pro komerční web Marketplace.

- **Pro komerční tržiště Microsoftu použijte standardní smlouvu?**

Pro zjednodušení procesu nákupu pro zákazníky a omezení právní složitosti pro dodavatele softwaru nabízí společnost Microsoft standardní smlouvu pro komerční tržiště Microsoftu, která usnadňuje transakce na webu Marketplace. Místo toho, aby si mohli vlastní podmínky a ujednání, můžou si komerční vydavatelé na webu Marketplace vybrat svůj software v rámci standardní smlouvy, kteří zákazníci potřebují Vet a přijmout jenom jednou. Standardní kontrakt najdete tady: https://go.microsoft.com/fwlink/?linkid=2041178 .

Výběrem možnosti použít standardní smlouvu pro komerční web Marketplace můžete vybrat, že se má používat standardní smlouva místo poskytování vlastních podmínek a ujednání.

![Pomocí zaškrtávacího políčka standardní smlouva](./media/use-standard-contract.png)

> [!NOTE]
> Po publikování nabídky pomocí standardní smlouvy pro komerční tržiště společnosti Microsoft nemůžete používat vlastní podmínky a ujednání. Jedná se o scénář "nebo". Vaše řešení si buď nabídnete v rámci standardní smlouvy, **nebo** podle vašich vlastních podmínek. Pokud chcete upravit podmínek standardní smlouvy, můžete to udělat prostřednictvím standardních změn smlouvy.

**Změny smlouvy Standard**

Změny smlouvy Standard umožňují vydavatelům vybrat standardní smluvní smlouvy pro jednoduchost a přizpůsobit si pro svůj produkt nebo firmu obory podmínek. Zákazníci si musí jenom projít změny smlouvy, pokud už zkontrolovali a přijali standardní kontrakt společnosti Microsoft.

K dispozici jsou dva druhy změn pro komerční vydavatele na webu Marketplace:

- Univerzální změny: tyto změny se aplikují všeobecně na standardní smlouvu pro všechny zákazníky. Univerzální změny se zobrazují každému zákazníkovi nabídky v toku nákupu. Zákazníci musí přijmout podmínky standardní smlouvy a dodatku, aby mohli používat vaši nabídku.
- Vlastní změny: tyto změny představují zvláštní změny standardní smlouvy, které jsou zaměřené na konkrétní zákazníky jenom prostřednictvím ID tenanta Azure. Vydavatelé můžou zvolit tenanta, na který chtějí cílit. S vlastními podmínkami změny v nákupním toku nabídky se zobrazí pouze zákazníci z tenanta.  Zákazníci musí přijmout podmínky smlouvy Standard a jejich změny, aby mohli používat vaši nabídku.

>[!NOTE]
> Tyto dva typy změn zásobníků jsou navzájem navzájem. Zákazníci, na které cílíte vlastní změny, získají při nákupu také univerzální změnu standardní smlouvy.

**Univerzální pozměňovací výrazy ke standardní smlouvě pro komerční tržiště Microsoftu**: do tohoto pole zadejte univerzální výrazy pro změnu. Pro každou nabídku můžete zadat jednu univerzální změnu. Do tohoto pole můžete zadat neomezený počet znaků. Tyto výrazy se zobrazí zákazníkům v AppSource, Azure Marketplace a/nebo Azure Portal během procesu zjišťování a nákupu.

**Vlastní výrazy pro změny standardní smlouvy pro komerční tržiště Microsoftu**: Začněte tím, že vyberete **Přidat vlastní výrazy pro změnu**. Pro každou nabídku můžete zadat až 10 vlastních podmínek pro změnu.

- **Vlastní výrazy pro změnu**: do pole vlastní výrazy pro změnu zadejte vlastní vlastní údaje. Do tohoto pole můžete zadat neomezený počet znaků. Pouze zákazníkům z ID tenanta, které zadáte pro tyto vlastní výrazy, se zobrazí vlastní výrazy změny v toku nákupu nabídky v Azure Portal.  
- **ID klientů** (povinné): každé vlastní změně může být až 20 ID klientů. Pokud přidáte vlastní změnu, musíte zadat aspoň jedno ID tenanta. ID tenanta identifikuje zákazníka v Azure. U zákazníka můžete požádat o toto ID a získat ho tak, že přejdete na portal.azure.com > Azure Active Directory > vlastnosti. Hodnota ID adresáře je ID tenanta (například 50c464d3-4930-494c-963c-1e951d15360e). ID tenanta organizace zákazníka můžete vyhledat také pomocí adresy URL svého názvu domény na adrese [moje Microsoft Azure a Microsoft 365 ID tenanta?](https://www.whatismytenantid.com).
- **Popis** (volitelné): Volitelně můžete zadat popis ID tenanta, který vám pomůže identifikovat zákazníka, kterému cílíte na změnu.

**Podmínky a ujednání**

Pokud chcete zadat vlastní podmínky a ujednání, můžete je vybrat do pole podmínky a ujednání. Do tohoto pole můžete zadat až 10 000 znaků textu. Pokud vaše podmínky a ujednání vyžadují delší popis, zadejte do tohoto pole odkaz na jednu adresu URL, kde se dají podmínky a ujednání najít. Zobrazí se zákazníkům jako aktivní odkaz.

Zákazníci musí tyto podmínky přijmout, aby mohli svoji nabídku vyzkoušet.

Než budete pokračovat, vyberte **Uložit koncept** .
