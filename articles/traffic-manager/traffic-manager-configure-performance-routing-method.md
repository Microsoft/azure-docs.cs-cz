---
title: Konfigurace metody směrování provozu výkonu pomocí Azure Traffic Manager | Dokumentace Microsoftu
description: Tento článek vysvětluje postup konfigurace Traffic Manageru směrovat provoz na koncový bod s nejnižší latenci
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: c378043a9e10a0aed5344ac3182af6163d217c7b
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140464"
---
# <a name="configure-the-performance-traffic-routing-method"></a>Konfigurace metody směrování provozu výkonu

Metody směrování provozu výkonu umožňuje směrovat provoz na koncový bod s nejnižší latenci v klientské síti. Datové centrum s nejnižší latencí je obvykle nejbližšímu v geografické vzdálenosti. Tuto metodu směrování provozu nemůže účtu v reálném čase změny v konfiguraci sítě nebo načíst.

##  <a name="to-configure-performance-routing-method"></a>Konfigurace metody směrování podle výkonu

1. V prohlížeči se přihlaste k webu [Azure Portal](http://portal.azure.com). Pokud ještě účet nemáte, můžete si zaregistrovat [zkušební verzi na měsíc zdarma](https://azure.microsoft.com/free/). 
2. Na panelu hledání na portálu vyhledejte **profily Traffic Manageru** a potom klikněte na název profilu, který chcete konfigurovat metodu směrování pro.
3. V **profil služby Traffic Manager** okno, ověřte, zda jsou k dispozici cloudovými službami a weby, které chcete zahrnout do vaší konfigurace.
4. V **nastavení** klikněte na tlačítko **konfigurace**a **konfigurace** okno vyplňte následujícím způsobem:
    1. Pro **nastavení metodu směrování provozu**, pro **metodu směrování** vyberte **výkonu**.
    2. Nastavte **nastavení sledování koncových bodů** shodné pro všechny každý koncový bod v rámci tohoto profilu následujícím způsobem:
        1. Vyberte příslušné **protokol**a zadejte **Port** číslo. 
        2. Pro **cesta** zadejte lomítkem */*. Monitorování koncových bodů, musíte zadat cestu a název souboru. Dopředné lomítko "/" je platná položka pro relativní cestu a znamená, že se soubor nachází v kořenovém adresáři (výchozí).
        3. V horní části stránky klikněte na tlačítko **Uložit**.
5.  Otestujte změny v konfiguraci následujícím způsobem:
    1.  Na panelu hledání na portálu vyhledejte název profilu Traffic Manageru a klikněte na profil Traffic Manageru ve výsledcích, který je zobrazeno.
    2.  V **Traffic Manageru** profilu okna, klikněte na tlačítko **přehled**.
    3.  **Profil služby Traffic Manager** okně se zobrazí název DNS váš nově vytvořený profil Traffic Manageru. Tímto lze všechny klienty (například tak, že přejdete do ní pomocí webového prohlížeče) získat směrovat na správný koncový bod jako Určuje typ směrování. V tomto případě směrují všechny požadavky na koncový bod s nejnižší latenci v klientské síti.
6. Jakmile váš profil Traffic Manageru funguje, upravte záznam DNS na autoritativního serveru DNS pro název domény vaší společnosti odkazovat na název domény Traffic Manageru.

![Konfigurace metody směrování provozu výkonu pomocí Traffic Manageru][1]

## <a name="next-steps"></a>Další postup

- Přečtěte si o [metodě váženého směrování provozu](traffic-manager-configure-weighted-routing-method.md).
- Přečtěte si o [metodě prioritního směrování](traffic-manager-configure-priority-routing-method.md).
- Přečtěte si o [metodě geografického směrování](traffic-manager-configure-geographic-routing-method.md).
- Zjistěte, jak [test nastavení Traffic Manageru](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png