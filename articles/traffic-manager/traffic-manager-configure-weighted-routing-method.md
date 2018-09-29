---
title: Konfigurace metody směrování provozu vážené kruhové dotazování pomocí služby Azure Traffic Manager | Dokumentace Microsoftu
description: Tento článek vysvětluje, jak vyrovnávat zatížení provozu pomocí metody kruhové dotazování v Traffic Manageru
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 6dca6de1-18f7-4962-bd98-6055771fab22
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 6637132481ee33d43ec2b747ba89a56983205ff2
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432439"
---
# <a name="configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Konfigurace metody směrování provozu vážený v Traffic Manageru

Běžným vzorem metodu směrování provozu je sada identické koncové body, jako je například cloud services a weby, a odesílat provoz do jednotlivých způsobem kruhové dotazování. Následující kroky popisují, jak konfigurovat metodu směrování provozu tohoto typu.

> [!NOTE]
> Služba Azure Web Apps již poskytuje funkce pro weby v rámci oblasti Azure (obsahuje několik datových center) pro vyrovnávání zatížení s kruhovým. Traffic Manager umožňuje určit metodu směrování provozu kruhovým dotazováním pro weby v různých datových centrech.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Konfigurace metody směrování provozu vážený

1. V prohlížeči se přihlaste k webu [Azure Portal](http://portal.azure.com). Pokud ještě účet nemáte, můžete si zaregistrovat [zkušební verzi na měsíc zdarma](https://azure.microsoft.com/free/). 
2. Na panelu hledání na portálu vyhledejte **profily Traffic Manageru** a potom klikněte na název profilu, který chcete konfigurovat metodu směrování pro.
3. V **profil služby Traffic Manager** okno, ověřte, zda jsou k dispozici cloudovými službami a weby, které chcete zahrnout do vaší konfigurace.
4. V **nastavení** klikněte na tlačítko **konfigurace**a **konfigurace** okno vyplňte následujícím způsobem:
    1. Pro **nastavení metodu směrování provozu**, ověřte, zda je metodu směrování provozu **vážená**. Pokud není, klikněte na tlačítko **vážená** z rozevíracího seznamu.
    2. Nastavte **nastavení sledování koncových bodů** shodné pro všechny každý koncový bod v rámci tohoto profilu následujícím způsobem:
        1. Vyberte příslušné **protokol**a zadejte **Port** číslo. 
        2. Pro **cesta** zadejte lomítkem */*. Monitorování koncových bodů, musíte zadat cestu a název souboru. Dopředné lomítko "/" je platná položka pro relativní cestu a znamená, že se soubor nachází v kořenovém adresáři (výchozí).
        3. V horní části stránky klikněte na tlačítko **Uložit**.
5. Otestujte změny v konfiguraci následujícím způsobem:
    1.  Na panelu hledání na portálu vyhledejte název profilu Traffic Manageru a klikněte na profil Traffic Manageru ve výsledcích, který je zobrazeno.
    2.  V **Traffic Manageru** profilu okna, klikněte na tlačítko **přehled**.
    3.  **Profil služby Traffic Manager** okně se zobrazí název DNS váš nově vytvořený profil Traffic Manageru. Tímto lze všechny klienty (například tak, že přejdete do ní pomocí webového prohlížeče) získat směrovat na správný koncový bod jako Určuje typ směrování. V tomto případě jsou všechny požadavky směrovány každý koncový bod v vždy střídat dokola.
6. Jakmile váš profil Traffic Manageru funguje, upravte záznam DNS na autoritativního serveru DNS pro název domény vaší společnosti odkazovat na název domény Traffic Manageru.

![Konfigurace metody směrování provozu vážený pomocí Traffic Manageru][1]

## <a name="next-steps"></a>Další postup

- Další informace o [metodu směrování provozu priority](traffic-manager-configure-priority-routing-method.md).
- Další informace o [metodu směrování provozu výkonu](traffic-manager-configure-performance-routing-method.md).
- Další informace o [metody geografického směrování](traffic-manager-configure-geographic-routing-method.md).
- Zjistěte, jak [test nastavení Traffic Manageru](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
