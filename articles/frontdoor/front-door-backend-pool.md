---
title: Back-endy a back-endové fondy v Azure Front Door| Dokumenty společnosti Microsoft
description: Tento článek popisuje, jaké back-endy a back-endové fondy jsou v konfiguraci front door.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 18b165d83bfa154348842542bd8323a40330aa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293474"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Back-endy a back-endové fondy v Azure Front Door
Tento článek popisuje koncepty o tom, jak mapovat nasazení aplikace pomocí Azure Front Door. Vysvětluje také různé termíny v konfiguraci Front Door kolem backendů aplikací.

## <a name="backends"></a>Back-endy
Back-end se rovná instanci nasazení aplikace v oblasti. Front Door podporuje back-endy Azure i jiných než Azure, takže oblast není omezená jen na oblasti Azure. Může to být také vaše místní datové centrum nebo instance aplikace v jiném cloudu.

Back-endy předních dveří odkazují na název hostitele nebo veřejnou IP adresu vaší aplikace, která může obsluhovat požadavky klientů. Back-endy by neměly být zaměňovány s vaší databázovou vrstvou, úrovní úložiště a tak dále. Back-endy by měly být považovány za veřejný koncový bod back-endu aplikace. Když přidáte back-end do fondu back-endu předních dveří, musíte také přidat následující:

- **Typ hostitele back-endu**. Typ prostředku, který chcete přidat. Přední dveře podporují automatické zjišťování backendů aplikací z aplikační služby, cloudové služby nebo úložiště. Pokud chcete jiný prostředek v Azure nebo dokonce back-end, který není Azure, vyberte **Vlastní hostitel**.

    >[!IMPORTANT]
    >Během konfigurace nejsou ověřování api, pokud back-end je nepřístupný z prostředí front door. Ujistěte se, že přední dveře mohou dosáhnout vašeho backendu.

- **Název hostitele předplatného a back-endu**. Pokud jste nevybrali **vlastní hostitel** pro typ back-endového hostitele, vyberte back-end výběrem příslušného předplatného a odpovídajícího názvu back-endového hostitele v uživatelském uživatelském nastavení.

- **Hlavička hostitele back-endu**. Hodnota hlavičky hostitele odeslaná back-endu pro každý požadavek. Další informace naleznete v [tématu Backend host header](#hostheader).

- **Priorita**. Přiřaďte priority různým back-endům, pokud chcete použít back-end primární služby pro veškerý provoz. Také poskytněte zálohy, pokud primární nebo zálohovací zálohy nejsou k dispozici. Další informace naleznete v [tématu Priority](front-door-routing-methods.md#priority).

- **Hmotnost**. Přiřaďte váhy různým back-endům k distribuci provozu mezi sadu back-endů, a to buď rovnoměrně, nebo podle koeficientů hmotnosti. Další informace naleznete v [tématu Váhy](front-door-routing-methods.md#weighted).

### <a name="backend-host-header"></a><a name = "hostheader"></a>Záhlaví hostitele back-endu

Požadavky předané frontovými dveřmi do back-endu zahrnují pole záhlaví hostitele, které back-end používá k načtení cílového prostředku. Hodnota pro toto pole obvykle pochází z identifikátoru URI back-endu a má hostitele a port.

Například požadavek pro `www.contoso.com` bude mít záhlaví hostitele www.contoso.com. Pokud ke konfiguraci back-endu používáte portál Azure, výchozí hodnota pro toto pole je název hostitele back-endu. Pokud je váš back-end contoso-westus.azurewebsites.net, na webu Azure Portal bude automaticky vyplněná hodnota pro hlavičku back-endového hostitele contoso-westus.azurewebsites.net. Pokud však použijete šablony Azure Resource Manager nebo jinou metodu bez explicitního nastavení tohoto pole, front door odešle název příchozího hostitele jako hodnotu hlavičky hostitele. Pokud byla žádost podána pro www\.contoso.com a váš back-end je contoso-westus.azurewebsites.net, který má\.prázdné pole záhlaví, front door nastaví hlavičku hostitele jako www contoso.com.

Většina back-endů aplikací (Azure Web Apps, úložiště objektů blob a cloudové služby) vyžaduje, aby záhlaví hostitele odpovídalo doméně back-endu. Front-endový hostitel, který směruje do back-endu, však bude používat jiný název hostitele, například www.contoso.net.

Pokud back-end vyžaduje, aby záhlaví hostitele odpovídalo názvu hostitele back-endu, ujistěte se, že hlavička back-endového hostitele obsahuje back-end ový název hostitele.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Konfigurace hlavičky back-endového hostitele pro back-end

Postup konfigurace pole **hlavičky back-endového hostitele** pro back-end v části back-endového fondu:

1. Otevřete prostředek předních dveří a vyberte back-endový fond s back-endem, který chcete konfigurovat.

2. Pokud jste tak neučinili, přidejte back-end nebo upravte existující.

3. Nastavte pole záhlaví back-endového hostitele na vlastní hodnotu nebo ponechte prázdné. Název hostitele pro příchozí požadavek bude použit jako hodnota hlavičky hostitele.

## <a name="backend-pools"></a>Back-endové fondy
Back-endový fond v front door odkazuje na sadu back-endů, které přijímají podobný provoz pro jejich aplikaci. Jinými slovy je to logické seskupení instancí aplikací po celém světě, které obdrží stejný provoz a reagují očekávaným chováním. Tyto back-endy jsou nasazeny v různých oblastech nebo ve stejné oblasti. Všechny back-endy mohou být v režimu aktivního/aktivního nasazení nebo v tom, co je definováno jako konfigurace Aktivní/Pasivní.

Back-endový fond definuje, jak by měly být různé back-endy vyhodnocovány pomocí sond stavu. Také definuje, jak dochází k vyrovnávání zatížení mezi nimi.

### <a name="health-probes"></a>Sondy stavu
Přední dveře odesílá pravidelné požadavky http/https sondy do každého z vašich nakonfigurovaných back-endů. Požadavky na sondu určují blízkost a stav každého back-endu pro vyrovnávání zatížení požadavků koncových uživatelů. Nastavení sondy stavu pro back-endový fond definují, jak jsme dotazování stav back-endů aplikace. Pro konfiguraci vyrovnávání zatížení jsou k dispozici následující nastavení:

- **Cesta:** Adresa URL použitá pro požadavky na sondu pro všechny back-endy v back-endovém fondu. Například pokud jeden z back-endů je contoso-westus.azurewebsites.net a cesta je nastavena na /probe/test.aspx, pak prostředí Front Door, za\:předpokladu, že protokol je nastaven na HTTP, odešle požadavky sondy stavu http //contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protokol**: Definuje, zda mají být odeslány požadavky na sondu stavu z předních dveří do back-endů pomocí protokolu HTTP nebo HTTPS.

- **Metoda**: Metoda HTTP, která má být použita pro odesílání sond stavu. Možnosti zahrnují GET nebo HEAD (výchozí).
    > [!NOTE]
    > Pro nižší zatížení a náklady na back-endy, Přední dveře doporučuje používat HEAD požadavky na zdravotní sondy.

- **Interval (sekundy)**: Definuje frekvenci sond stavu do back-endů nebo intervaly, ve kterých každé prostředí předních dveří odešle sondu.

    >[!NOTE]
    >Pro rychlejší převzetí služeb při selhání nastavte interval na nižší hodnotu. Čím nižší je hodnota, tím vyšší je objem sondy stavu, který back-endy obdrží. Například pokud je interval nastaven na 30 sekund s say, 100 front door POP globálně, každý back-end obdrží přibližně 200 požadavků sondy za minutu.

Další informace naleznete v tématu [Sondy stavu](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Nastavení vyrovnávání zatížení
Nastavení vyrovnávání zatížení pro back-endový fond definují, jak vyhodnocujeme sondy stavu. Tato nastavení určují, zda je back-end v pořádku nebo není v pořádku. Také zkontrolovat, jak zatížení zůstatku provoz mezi různými back-endy v back-endu fondu. Pro konfiguraci vyrovnávání zatížení jsou k dispozici následující nastavení:

- **Velikost vzorku**. Určuje, kolik vzorků sond stavu musíme zvážit pro hodnocení stavu back-endu.

- **Úspěšná velikost vzorku**. Definuje velikost vzorku, jak již bylo zmíněno, počet úspěšných vzorků potřebných k volání back-endu v pořádku. Předpokládejme například, že interval sondy stavu předních dveří je 30 sekund, velikost vzorku je 5 a úspěšná velikost vzorku je 3. Pokaždé, když vyhodnotíme sondy stavu pro back-end, podíváme se na posledních pět vzorků za 150 sekund (5 x 30). Nejméně tři úspěšné sondy jsou nutné deklarovat back-end jako v pořádku.

- **Citlivost na latenci (další latence).** Definuje, zda chcete, aby přední dveře odeslalo požadavek back-endům v rozsahu citlivosti měření latence nebo předaly požadavek nejbližšímu back-endu.

Další informace naleznete v [tématu Metoda směrování založená na nejméně latenci](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Další kroky

- [Vytvoření profilu předních dveří](quickstart-create-front-door.md)
- [Jak fungují přední dveře](front-door-routing-architecture.md)