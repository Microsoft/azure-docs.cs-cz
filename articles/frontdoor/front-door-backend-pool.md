---
title: Back-endy a back-endové fondy v Azure – přední dveře | Microsoft Docs
description: Tento článek popisuje, jaké back-endy a back-end fondy jsou v konfiguraci front-endu.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4beba141fec7a819df52e4c3a669312a4ad76998
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449298"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Back-endy a back-endové fondy v Azure front-endu
Tento článek popisuje informace o tom, jak namapovat nasazení webové aplikace s využitím front-dveří Azure. Vysvětluje také různé terminologií, které se používají v konfiguraci front-endu v případě back-endy aplikace.

## <a name="backends"></a>Back-endy
Back-end odkazuje na nasazení webové aplikace v oblasti. Přední dvířka podporují prostředky Azure i mimo Azure ve fondu back-endu. Aplikace může být buď v místním datovém centru, nebo umístěná v jiném poskytovateli cloudu.

Back-endové přední dveře označují název hostitele nebo veřejnou IP adresu vaší aplikace, která obsluhuje požadavky klientů. Back-endy by se neměly zaměňovat s vaší databázovou vrstvou, vrstvou úložiště a tak dále. Back-endy by se mělo zobrazit jako veřejný koncový bod pro back-end aplikace. Když přidáte back-end do front back-endu back-endu, je nutné přidat také následující:

- **Typ hostitele back-endu**. Typ prostředku, který chcete přidat. Přední dvířka podporují automatické zjišťování back-endu vaší aplikace ze služby App Service, cloudové služby nebo úložiště. Pokud chcete jiný prostředek v Azure nebo dokonce back-end, vyberte **vlastní hostitel**.

    >[!IMPORTANT]
    >Při konfiguraci rozhraní API neověřuje, jestli je back-end nepřístupný z prostředí front-endu. Ujistěte se, že se přední dveře dostanou k vašemu back-endu.

- **Název hostitele a název hostitele back-endu**. Pokud jste pro typ hostitele back-end nevybrali **vlastního hostitele** , vyberte back-end tak, že vyberete příslušné předplatné a odpovídající název hostitele back-endu v uživatelském rozhraní.

- **Hlavička hostitele back-endu**. Hodnota hlavičky hostitele, která se pošle back-endu pro každý požadavek. Další informace najdete v tématu [Hlavička hostitele back-endu](#hostheader).

- **Priorita**. Přiřaďte priority k různým back-endu, pokud chcete použít primární back-end službu pro veškerý provoz. Pokud primární nebo záložní back-endy nejsou k dispozici, zadejte také zálohy. Další informace najdete v tématu [Priorita](front-door-routing-methods.md#priority).

- **Váha**. Přiřaďte váhy k různým back-endu pro distribuci provozu napříč sadou back-endu, a to buď rovnoměrně, nebo podle váhy. Další informace najdete v tématu [váhy](front-door-routing-methods.md#weighted).

### <a name="backend-host-header"></a><a name = "hostheader"></a>Hlavička hostitele back-endu

Požadavky předané předními dveřmi do back-endu obsahují pole hlavičky hostitele, které back-end používá k načtení cílového prostředku. Hodnota tohoto pole obvykle pochází z identifikátoru URI back-endu, který obsahuje hlavičku a port hostitele.

Například žádost, která byla vytvořena pro, `www.contoso.com` bude mít hlavičku hostitele www.contoso.com. Pokud ke konfiguraci back-endu použijete Azure Portal, výchozí hodnota tohoto pole je název hostitele back-endu. Pokud je váš back-end contoso-westus.azurewebsites.net, v Azure Portal bude automaticky vyplněná hodnota pro hlavičku hostitele back-end contoso-westus.azurewebsites.net. Pokud však použijete šablony Azure Resource Manager nebo jinou metodu bez explicitního nastavení tohoto pole, odešle fronta název příchozího hostitele jako hodnotu pro hlavičku hostitele. Pokud se žádost nastavila pro \. webcontoso.com a vaše back-end je contoso-westus.azurewebsites.NET, který má prázdné pole hlavičky, nastaví se na začátku jako na webové \. contoso.com záhlaví hostitele.

Většina back-endy aplikace (Azure Web Apps, BLOB Storage a Cloud Services) vyžaduje, aby Hlavička hostitele odpovídala doméně back-endu. Nicméně hostitel s front-end, který směruje na váš back-end, bude používat jiný název hostitele, například www.contoso.net.

Pokud váš back-end vyžaduje, aby Hlavička hostitele odpovídala názvu hostitele back-endu, ujistěte se, že Hlavička hostitele back-endu obsahuje název hostitele back-endu.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Konfigurace hlavičky hostitele back-endu pro back-end

Konfigurace pole **hlavičky hostitele back-endu** pro back-end v části back-end fondu:

1. Otevřete prostředek front-endu a vyberte back-end fond s back-endu, který chcete nakonfigurovat.

2. Pokud jste to ještě neudělali, můžete přidat back-end, nebo upravit stávající.

3. Nastavte pole hlavička back-endu hostitele na vlastní hodnotu nebo nechte prázdné. Název hostitele příchozího požadavku bude použit jako hodnota hlavičky hostitele.

## <a name="backend-pools"></a>Back-endové fondy
Back-end fond v předních dveřích odkazuje na sadu back-endu, které pro svou aplikaci přijímají podobný provoz. Jinými slovy je logické seskupení instancí vaší aplikace po celém světě, které obdrží stejný provoz a reagovat s očekávaným chováním. Tyto back-endy se nasazují v různých oblastech nebo ve stejné oblasti. Všechny back-endy můžou být v režimu aktivního/aktivního nasazení nebo jaké jsou definované jako aktivní nebo pasivní konfigurace.

Back-end fond definuje, jak by se měly vyhodnocovat různé back-endy prostřednictvím sond stavu. Definuje také, jak mezi nimi dochází k vyrovnávání zatížení.

### <a name="health-probes"></a>Sondy stavu
Přední dveře odesílají pravidelné zkušební požadavky HTTP/HTTPS na všechny vaše nakonfigurované back-endy. Požadavky na testování určují vzdálenost a stav každého back-endu pro vyrovnávání zatížení vašich požadavků koncových uživatelů. Nastavení sond stavu pro back-end fond definují, jak se dotazuje na stav back-endu aplikace. Pro konfiguraci vyrovnávání zatížení jsou k dispozici následující nastavení:

- **Cesta**: adresa URL, která se používá k testování požadavků pro všechny back-endy ve fondu back-endu. Například pokud je jeden z vašich back-endu contoso-westus.azurewebsites.net a cesta je nastavená na/PROBE/test.aspx, pak na front-endové prostředí, za předpokladu, že je protokol nastavený na HTTP, bude odesílat požadavky sondy stavu do http \: //Contoso-westus.azurewebsites.NET/PROBE/test.aspx.

- **Protokol**: Určuje, jestli se mají odesílat požadavky sondy stavu z předních dveří do back-endu s protokolem HTTP nebo HTTPS.

- **Metoda**: metoda HTTP, která se má použít pro odesílání sond stavu. Mezi možnosti patří GET nebo HEAD (výchozí).
    > [!NOTE]
    > V případě nižšího zatížení a nákladů na back-endy doporučuje přední dveře použití požadavků hlav pro sondy stavu.

- **Interval (sekundy)**: definuje četnost sond stavu pro vaše back-endy nebo intervaly, ve kterých každé z prostředí front-dveří odesílá test.

    >[!NOTE]
    >V případě rychlejšího převzetí služeb při selhání nastavte interval na nižší hodnotu. Čím nižší hodnota, tím vyšší je hlasitost sondy stavu, kterou vaše back-endy obdrží. Pokud je například interval nastaven na 30 sekund s vyslovením, 100 přední dvířka (pop) se zobrazí globálně, každý back-end bude dostávat asi 200 zkušebních požadavků za minutu.

Další informace najdete v tématu [sondy stavu](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Nastavení vyrovnávání zatížení
Nastavení vyrovnávání zatížení pro back-end fond definuje, jak vyhodnocujeme sondy stavu. Tato nastavení určují, jestli je back-end v pořádku nebo není v pořádku. Také kontrolují, jak vyrovnávat zatížení provozu mezi různými back-endy ve fondu back-endu. Pro konfiguraci vyrovnávání zatížení jsou k dispozici následující nastavení:

- **Velikost vzorku**. Určuje, kolik ukázek sond stavu musíme zvážit při vyhodnocování stavu back-endu.

- **Velikost vzorku je úspěšná**. Definuje velikost vzorku, jak je uvedeno výše, počet úspěšných ukázek potřebných k volání back-endu v pořádku. Předpokládejme například, že interval sondy stavů front je 30 sekund, velikost vzorku je 5 a úspěšná velikost vzorku je 3. Pokaždé, když vyhodnocujeme sondy stavu pro váš back-end, podíváme se na posledních pět vzorků za více než 150 sekund (5 × 30). K deklaraci back-endu jako v pořádku se vyžadují aspoň tři úspěšné sondy.

- **Citlivost na latenci (další latence)**. Definuje, jestli chcete, aby přední dvířka odesílala požadavek na back-endy v rozsahu citlivosti měření latence, nebo požadavek předá do nejbližšího back-endu.

Další informace najdete v tématu [minimální Metoda směrování na základě latence](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Další kroky

- [Vytvoření profilu front-dveří](quickstart-create-front-door.md)
- [Jak fungují přední dveře](front-door-routing-architecture.md)
