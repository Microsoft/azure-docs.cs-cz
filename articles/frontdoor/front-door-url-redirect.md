---
title: Azure přední dveře Service – adresa URL přesměrování | Dokumentace Microsoftu
description: Tento článek pomůže pochopit, jak Azure branou služba podporuje přesměrování adresy URL pro jejich trasy, pokud nakonfigurované.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 3d77a16d24a1a843b39d97904a675518c43a525a
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332686"
---
# <a name="url-redirect"></a>Adresa URL pro přesměrování
Pro přesměrování přenosu dat můžete použít Azure branou služby. Můžete přesměrovat provoz na více úrovních (protokol, název hostitele, cesta, řetězec dotazu) a všechny funkce, je možné nakonfigurovat pro jednotlivých mikroslužeb, jako je přesměrování na základě cest. To zjednodušuje konfiguraci aplikací, optimalizuje využití prostředků a podporuje nové scénáře přesměrování, včetně globální a na základě cest přesměrování.
</br>

![Adresa URL přesměrování branou Azure][1]

## <a name="redirection-types"></a>Typy přesměrování
Typ přesměrování nastaví stavový kód odpovědi pro klienty a pochopit účel přesměrování. Jsou podporovány následující typy přesměrování:

- **301 (trvale přesunuto)** : Označuje, že cílový prostředek přiřazen nový identifikátor URI trvalé a chcete použít jeden z uzavřených identifikátory URI by mělo být všechny budoucí odkazy na tento prostředek. Použijte 301 stavový kód HTTP pro přesměrování protokolu HTTPS. 
- **302 (Najít)** : Označuje, že cílový prostředek dočasně nachází v jiný identifikátor URI. Vzhledem k tomu, že v některých případech může změnit přesměrování, klient by měla dál používat pro budoucí požadavky efektivní identifikátor URI požadavku.
- **307 (dočasné přesměrování)** : Označuje, že cílový prostředek je dočasně umístěn pod jiný identifikátor URI a uživatelský agent nesmí změnit metodu žádosti v případě, že provádí automatické přesměrování na tento identifikátor URI. Protože v průběhu času měnit přesměrování, můžete klienta by mělo být pokračovat v používání původní efektivní identifikátor URI požadavku pro budoucí požadavky.
- **308 (trvalé přesměrování)** : Označuje, že cílový prostředek přiřazen nový identifikátor URI trvalé a chcete použít jeden z uzavřených identifikátory URI by mělo být všechny budoucí odkazy na tento prostředek. Klienti s odkazem na úpravy funkce, by mělo být automaticky znovu připojit odkazy na efektivního identifikátor URI žádosti do jedné nebo více nových odkazů odeslané serverem, kde je to možné.

## <a name="redirection-protocol"></a>Přesměrování protokolu
Můžete nastavit na protokol, který se použije pro přesměrování. Díky tomu pro jeden z nejběžnějších případech použití funkce přesměrování, který je k nastavení protokolu HTTP na HTTPS přesměrování.

- **Pouze HTTPS**: Nastavte protokol na protokol HTTPS pouze, pokud chcete pro přesměrování přenosu dat z protokolu HTTP na HTTPS. Služba Azure branou doporučuje, že byste měli nastavit přesměrování na HTTPS pouze.
- **Jenom HTTP**: To přesměruje příchozího požadavku HTTP. Pomocí této hodnoty pouze v případě, že chcete zachovat provoz protokolu HTTP, který je, bez šifrování.
- **Žádost o shodu**: Tato možnost ponechá protokol používá příchozího požadavku. Tak požadavek HTTP zůstane HTTP a HTTPS příspěvek přesměrování zůstává požadavek HTTPS.

## <a name="destination-host"></a>Cílový hostitel
Jako součást konfigurace přesměrování směrování můžete také změnit název hostitele nebo doménu pro přesměrování požadavku. Můžete nastavit toto pole pro název hostitele v adrese URL pro přesměrování nebo jinak zachovat název hostitele z příchozího požadavku. Takže použití tohoto pole můžete přesměrovat všechny požadavky odeslané https://www.contoso.com/ * k https://www.fabrikam.com/ *.

## <a name="destination-path"></a>Cílová cesta
Pro případy, ve kterém mají být nahrazeny segment cesty adresy URL jako součást přesměrování můžete nastavit toto pole s hodnotou novou cestu. V opačném případě můžete zachovat hodnotu cesty jako součást přesměrování. Takže použití tohoto pole můžete přesměrovat všechny požadavky odeslané na https://www.contoso.com/ * k https://www.contoso.com/redirected-site.

## <a name="query-string-parameters"></a>Parametry řetězce dotazu
Můžete také nahradit parametry řetězce dotazu v adrese URL přesměrovaného. Aby bylo možné nahradit všechny existující řetězce dotazu z příchozí adrese URL požadavku, nastavte toto pole na "Nahradit" a potom nastavte na odpovídající hodnotu. V opačném případě můžete zachovat původní sadu řetězců dotazu nastavením pole "Zachování". Jako příklad použití tohoto pole můžete přesměrovat veškerý provoz odeslaný na https://www.contoso.com/foo/bar k https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F. 

## <a name="destination-fragment"></a>Určení fragmentu
Fragment cíl je část adresy URL za "#", obvykle pomocí prohlížeče přejít na určitou část na stránku. Můžete nastavit toto pole pro přidání fragmentu k adrese URL přesměrování.

## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png