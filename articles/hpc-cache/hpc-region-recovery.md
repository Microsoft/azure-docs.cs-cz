---
title: Místní redundance a obnovení služeb při selhání s azure hpc cache
description: Techniky pro poskytování možností převzetí služeb při selhání pro zotavení po havárii pomocí mezipaměti Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 21074ae6bc4959da031bc7065cd7d0639ec2a14f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537266"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>Použití více mezipamětí pro místní obnovení převzetí služeb při selhání

Každá instance mezipaměti HPC Azure běží v rámci konkrétního předplatného a v jedné oblasti. To znamená, že pracovní postup mezipaměti může být pravděpodobně přerušen, pokud má oblast úplný výpadek.

Tento článek popisuje strategii ke snížení rizika narušení práce pomocí druhé oblasti pro převzetí služeb při selhání mezipaměti.

Klíč používá back-endové úložiště, které je přístupné z více oblastí. Toto úložiště může být buď místní systém NAS s odpovídající podporou DNS, nebo úložiště objektů blob Azure, které se nachází v jiné oblasti než v mezipaměti.

Jak váš pracovní postup pokračuje ve vaší primární oblasti, data se ukládají do dlouhodobého úložiště mimo oblast. Pokud oblast mezipaměti přestane být k dispozici, můžete vytvořit duplicitní instanci mezipaměti Azure HPC v sekundární oblasti, připojit se ke stejnému úložišti a pokračovat v práci z nové mezipaměti.

## <a name="planning-for-regional-failover"></a>Plánování regionálního převzetí služeb při selhání

Chcete-li nastavit mezipaměť připravenou pro možné převzetí služeb při selhání, postupujte takto:

1. Ujistěte se, že vaše back-endové úložiště je přístupné v druhé oblasti.
1. Při plánování vytvoření primární instance mezipaměti byste se měli také připravit na replikaci tohoto procesu instalace v druhé oblasti. Zahrnout tyto položky:

   1. Struktura virtuální sítě a podsítě
   1. Kapacita mezipaměti
   1. Podrobnosti o cíli úložiště, názvy a cesty oboru názvů
   1. Podrobnosti o klientských počítačích, pokud jsou umístěny ve stejné oblasti jako mezipaměť
   1. Příkaz Připojit pro použití klienty mezipaměti

   > [!NOTE]
   > Azure HPC Cache lze vytvořit programově, buď prostřednictvím [šablony Azure Resource Manager](../azure-resource-manager/templates/overview.md) nebo přímým přístupem k jeho rozhraní API. Podrobnosti vám poskytne tým Azure HPC Cache.

## <a name="failover-example"></a>Příklad převzetí služeb při selhání

Jako příklad si představte, že chcete najít azure hpc mezipaměť v oblasti Azure east US. Bude přistupovat k datům uloženým v místním datovém centru.

Mezipaměť v oblasti Západní USA 2 můžete použít jako zálohu převzetí služeb při selhání.

Při vytváření mezipaměti v USA – východ připravte druhou mezipaměť pro nasazení v západní ČÁSTI USA 2. K automatizaci této přípravy můžete použít skriptování nebo šablony.

V případě selhání celé oblasti v USA – východ vytvořte mezipaměť, kterou jste připravili v oblasti Západní USA 2.

Po vytvoření mezipaměti přidejte cíle úložiště, které odkazují na stejná místní úložiště dat, a používejte stejné agregované cesty oboru názvů jako cíle úložiště staré mezipaměti.

Pokud jsou ovlivněny původní klienti, vytvořte nové klienty v oblasti Západní USA 2 pro použití s novou mezipamětí.

Všichni klienti budou muset připojit novou mezipaměť, i v případě, že klienti nebyli ovlivněni výpadkem oblasti. Nová mezipaměť má různé adresy připojení od staré.

## <a name="learn-more"></a>Další informace

Průvodce architekturou aplikací Azure obsahuje další informace o tom, jak [se zotavit z přerušení služby v rámci celé oblasti](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>).
