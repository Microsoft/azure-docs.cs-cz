---
title: Služba DNS v Azure zásobníku | Microsoft Docs
description: Pomocí DNS v Azure zásobníku
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: 4e854a2751ce366e3ca3a353487f2c972401c248
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196521"
---
# <a name="using-dns-in-azure-stack"></a>Pomocí DNS v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Zásobník Azure podporuje následující funkce systému DNS (Domain Name):

* Překlad názvů hostitelů DNS
* Vytváření a správě zóny DNS a záznamů pomocí rozhraní API

## <a name="support-for-dns-hostname-resolution"></a>Podpora pro překlad DNS názvu hostitele

Můžete zadat popisek názvu domény DNS pro prostředky veřejné IP adresy. Používá Azure zásobníku *domainnamelabel.location*. cloudapp.azurestack.external pro název popisku a mapy jej do veřejných IP adres v zásobníku Azure spravované servery DNS.

Například pokud vytvoříte prostředek veřejné IP s **contoso** jako popisek názvu domény v umístění místní zásobník Azure [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN)  **contoso.Local.cloudapp.azurestack.external** přeloží na veřejnou IP adresu prostředku. Chcete-li vytvořit vlastní doménu záznam CNAME, který odkazuje na veřejnou IP adresu v zásobníku Azure můžete použít tento plně kvalifikovaný název domény.

Další informace o překladu názvů, naleznete [překlad DNS](https://docs.microsoft.com/en-us/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) článku.

> [!IMPORTANT]
> Každý popisek názvu domény, který vytvoříte, musí být jedinečný v rámci svého umístění Azure zásobníku.

Na další obrazovce zaznamenat ukazuje **vytvoření veřejné IP adresy** dialogové okno pro vytvoření veřejné IP adresy pomocí portálu.

![Vytvoření veřejné IP adresy](media/azure-stack-whats-new-dns/image01.png)

**Příklad scénáře**

Máte Vyrovnávání zatížení zpracování žádostí z webové aplikace. Za zatížení je vyrovnávání webu běžícím na jeden nebo více virtuálních počítačů. Můžete přistupovat pomocí názvu DNS, místo IP adresu webu Vyrovnávání zatížení sítě.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>Vytváření a Správa zón DNS a záznamů pomocí rozhraní API

Můžete vytvořit a spravovat zóny DNS a záznamy v zásobníku Azure.

Zásobník Azure poskytuje službu DNS jako Azure, pomocí rozhraní API, které jsou konzistentní s rozhraními API Azure DNS.  Hostování domény do Azure DNS zásobníku, můžete spravovat svoje záznamy DNS pomocí stejné přihlašovací údaje, rozhraní API a nástroje. Můžete také použít stejné fakturaci a podporu jako jinými službami Azure.

Infrastruktura Azure zásobníku DNS je kompaktnější než Azure. Velikost a umístění Azure zásobníku nasazení bude mít vliv na DNS oboru, rozsah a výkon. Také to znamená, výkonu, dostupnosti, globální distribuce a vysokou dostupností se může lišit od nasazení k nasazení.

## <a name="comparison-with-azure-dns"></a>Porovnání s Azure DNS

Služba DNS v zásobníku Azure je podobná DNS v Azure, ale existují hlavní výjimky, které je třeba pochopit.

* **Nepodporuje záznamů AAAA**

    Azure zásobník nepodporuje záznamů AAAA, protože zásobník Azure nepodporuje adresy IPv6.  Toto je zásadní rozdíl mezi DNS v Azure a Azure zásobníku.
* **Není více klientů**

    Služba DNS v zásobníku Azure není více klientů. Každý klient nemůže vytvořit stejné zóny DNS. Pouze první předplatné, které se pokusí o vytvoření zóny úspěšné a neúspěšné následných žádostí.  Jde o známý problém a klíče rozdíl mezi Azure a Azure DNS zásobníku. Tento problém bude vyřešen v budoucí verzi.
* **Značky, metadat a značky etag binárním rozsáhlým**

    Existují malé rozdíly v tom, jak Azure zásobník zpracovává značky, metadata, značky etag binárním rozsáhlým a omezení.

Další informace o službě Azure DNS najdete v tématu [DNS zóny a zaznamenává](../../dns/dns-zones-records.md).

### <a name="tags-metadata-and-etags"></a>Značky, metadat a značky etag binárním rozsáhlým

**Značky**

Azure DNS zásobníku podporuje pomocí Azure Resource Manager značek na prostředky zóny DNS. Nepodporuje značky na sady záznamů DNS, i když jako alternativu, metadata, je podporovaný na záznam DNS nastaví, jak je popsáno dále.

**Metadata**

Jako alternativu k značky sady záznamů DNS zásobník Azure podporuje, zadávání poznámek k sad záznamů pomocí 'metadat'. Podobně jako u značky, metadata vám umožní přidružit dvojice název hodnota s každou sadu záznamů. Například to může být užitečné k zaznamenání účel každá sada záznamů. Na rozdíl od značky metadata nelze použít k poskytují filtrované zobrazení faktury Azure a nelze zadat v zásadách Azure Resource Manager.

**Značky etag binárním rozsáhlým**

Předpokládejme, že dvě osoby nebo dva procesy zkuste upravit záznam DNS ve stejnou dobu. Které z nich wins? A Vítěz vědět, že jste se přepsat změny vytvořený jiným uživatelem?

Azure DNS zásobníku používá značky etag binárním rozsáhlým bezpečně zpracování souběžných změny do stejného zdroje. Značky etag binárním rozsáhlým jsou oddělené od Azure Resource Manager, značky'. Všechny prostředky DNS (zóny nebo sady záznamů) má Etag s ním spojená. Vždy, když se načte prostředek, je také načíst jeho Značka Etag. Při aktualizaci prostředek, můžete předat zpět značku Etag, můžete Azure zásobníku DNS ověřte, že značku Etag na serveru odpovídá. Vzhledem k tomu, že každá aktualizace na prostředek, výsledkem Značka Etag se znovu vygeneruje, neshoda značek Etag označuje, že došlo ke změně souběžných. Značky etag binárním rozsáhlým mohou sloužit také při vytváření nového prostředku zajistit, že prostředek již neexistuje.

Ve výchozím nastavení používá Azure PowerShell DNS zásobníku značky etag binárním rozsáhlým blokovat souběžných změny zón a sady záznamů. Volitelné *-přepsat* přepínač můžete použít k potlačení kontroly Značka Etag, v takovém případě všechny souběžných budou přepsána změny, které mají došlo k chybě.

Na úrovni rozhraní API REST služby Azure zásobníku DNS jsou značky etag binárním rozsáhlým zadán pomocí hlavičky protokolu HTTP. V následující tabulce je uveden jejich chování:

| Záhlaví | Chování|
|--------|---------|
| Žádný   | PUT vždy úspěšné (žádná značka Etag kontrola)|
| If-match| PUT pouze úspěšná, pokud prostředek existuje a Značka Etag odpovídá|
| If-match *| PUT pouze úspěšná, pokud existuje prostředek|
| If-none-match *| PUT pouze úspěšná, pokud prostředek neexistuje.|

### <a name="limits"></a>Omezení

Následující výchozí omezení platí při použití Azure DNS zásobníku:

| Prostředek| Výchozí omezení|
|---------|--------------|
| Zóny na předplatné| 100|
| Sady záznamů na zóny| 5000|
| Záznamů na sady záznamů| 20|

## <a name="next-steps"></a>Další postup

[Představení IDN pro Azure zásobníku](azure-stack-understanding-dns.md)
