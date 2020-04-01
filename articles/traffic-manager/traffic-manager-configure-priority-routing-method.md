---
title: Kurz – Konfigurace směrování prioritních přenosů pomocí Azure Traffic Manageru
description: Tento kurz vysvětluje, jak nakonfigurovat metodu směrování prioritního provozu ve Správci provozu
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: ca223a19ff7ddeae95878f941f3cf295664e62b4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76938732"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Kurz: Konfigurace metody směrování prioritních přenosů ve Správci provozu

Bez ohledu na režim webu už weby Azure poskytují funkce převzetí služeb při selhání pro weby v rámci datového centra (označované také jako oblast). Traffic Manager poskytuje převzetí služeb při selhání pro weby v různých datových centrech.

Běžným vzorem pro převzetí služeb při selhání je odesílání přenosů primární službě a poskytování sady identických služeb zálohování pro převzetí služeb při selhání. Následující kroky vysvětlují, jak nakonfigurovat toto přednostní převzetí služeb při selhání pomocí cloudových služeb azure a webů:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Konfigurace metody směrování prioritního provozu

1. V prohlížeči se přihlaste k webu [Azure Portal](https://portal.azure.com). Pokud ještě účet nemáte, můžete si zaregistrovat [zkušební verzi na měsíc zdarma](https://azure.microsoft.com/free/). 
2. Na vyhledávacím panelu portálu vyhledejte **profily traffic manageru** a klikněte na název profilu, pro který chcete nakonfigurovat metodu směrování.
3. V okně **profilu Traffic Managerovte** ověřte, zda jsou k dispozici cloudové služby i weby, které chcete zahrnout do konfigurace.
4. V části **Nastavení** klikněte na **Konfigurace**a v okně **Konfigurace** postupujte takto:
    1. V **případě nastavení metody směrování provozu**ověřte, zda je metoda směrování provozu **Priorita**. Pokud tomu tak není, klikněte v rozevíracím seznamu na **Prioritu.**
    2. Nastavení **monitoru koncového bodu** je shodná pro všechny koncové hodnoty v rámci tohoto profilu následujícím způsobem:
        1. Vyberte příslušný **protokol**a zadejte číslo **portu.** 
        2. Pro **cestu** zadejte */* lomítko . Chcete-li sledovat koncové body, musíte zadat cestu a název souboru. Lomítko "/" je platná položka pro relativní cestu a znamená, že soubor je v kořenovém adresáři (výchozí).
        3. V horní části stránky klikněte na **Uložit**.
5. V části **Nastavení** klepněte na **položku Koncové body**.
6. V okně **Koncové body** zkontrolujte pořadí priorit pro koncové body. Když vyberete metodu **směrování prioritního** provozu, záleží na pořadí vybraných koncových bodů. Ověřte pořadí priorit koncových bodů.  Primární koncový bod je nahoře. Zkontrolujte pořadí, ve které se zobrazí. všechny požadavky budou směrovány do prvního koncového bodu a pokud Traffic Manager zjistí, že není v pořádku, provoz automaticky převezme služby při selhání do dalšího koncového bodu. 
7. Chcete-li změnit pořadí priorit koncového bodu, klepněte na koncový bod a v zobrazeném okně **Koncový bod** klikněte na **upravit** a podle potřeby změňte hodnotu **Priority.** 
8. Kliknutím na **Uložit** uložte změnu nastavení koncového bodu.
9. Po dokončení změn konfigurace klikněte v dolní části stránky na **Uložit.**
10. Otestujte změny v konfiguraci následujícím způsobem:
    1.  Na vyhledávacím panelu portálu vyhledejte název profilu Traffic Manageru a ve výsledcích, které se zobrazí, klikněte na profil Traffic Manager.
    2.  V okně **Profil traffic manageru** klepněte na **položku Přehled**.
    3.  Okno **profilu Traffic Manager** zobrazuje název DNS nově vytvořeného profilu traffic manageru. To může být použito všemi klienty (například přechodem na něj pomocí webového prohlížeče) získat směrovány na správný koncový bod, jak je určeno typu směrování. V tomto případě jsou všechny požadavky směrovány do prvního koncového bodu a pokud Traffic Manager zjistí, že není v pořádku, provoz automaticky převezme služby při selhání k dalšímu koncovému bodu.
11. Jakmile profil Traffic Manageru funguje, upravte záznam DNS na autoritativním serveru DNS tak, aby název domény vaší společnosti ukazoval na název domény Traffic Manageru.

![Konfigurace metody směrování prioritního provozu pomocí Traffic Manageru][1]

## <a name="next-steps"></a>Další kroky


- Přečtěte si o [metodě váženého směrování provozu](traffic-manager-configure-weighted-routing-method.md).
- Informace o [metodě směrování výkonu](traffic-manager-configure-performance-routing-method.md).
- Přečtěte si o [metodě geografického směrování](traffic-manager-configure-geographic-routing-method.md).
- Přečtěte si, jak [otestovat nastavení traffic manageru](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png