---
title: Regionální redundance a převzetí služeb při selhání s využitím mezipaměti HPC Azure
description: Techniky pro poskytování funkcí převzetí služeb při selhání pro zotavení po havárii s využitím mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 9159807f55ae52393b8fccec339fcc94c3e4ebb0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87061381"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>Použití více mezipamětí pro místní obnovení převzetí služeb při selhání

Každá instance mezipaměti HPC Azure se spouští v rámci konkrétního předplatného a v jedné oblasti. To znamená, že váš pracovní postup mezipaměti může být přerušený, pokud má oblast plný výpadek.

Tento článek popisuje strategii pro snížení rizika přerušení práce pomocí druhé oblasti pro převzetí služeb při selhání mezipaměti.

Klíč používá back-end úložiště, které je přístupné z více oblastí. Toto úložiště může být místní systém NAS s příslušnou podporou DNS nebo úložištěm objektů BLOB v Azure, které se nachází v jiné oblasti než mezipaměť.

V případě, že váš pracovní postup pokračuje v primární oblasti, data se ukládají v dlouhodobém úložišti mimo oblast. Pokud oblast mezipaměti nebude k dispozici, můžete vytvořit duplicitní instanci Azure HPC cache v sekundární oblasti, připojit se ke stejnému úložišti a pokračovat v práci z nové mezipaměti.

## <a name="planning-for-regional-failover"></a>Plánování regionálního převzetí služeb při selhání

Pokud chcete nastavit mezipaměť, která je připravená na možné převzetí služeb při selhání, postupujte podle těchto kroků:

1. Ujistěte se, že je vaše back-endové úložiště dostupné ve druhé oblasti.
1. Když plánujete vytvořit instanci primární mezipaměti, měli byste také připravit na replikaci tohoto procesu instalace ve druhé oblasti. Zahrnout tyto položky:

   1. Struktura virtuální sítě a podsítě
   1. Kapacita mezipaměti
   1. Podrobnosti cíle úložiště, názvy a cesty oboru názvů
   1. Podrobnosti o klientských počítačích, pokud se nacházejí ve stejné oblasti jako mezipaměť
   1. Příkaz Mount pro použití klienty mezipaměti

   > [!NOTE]
   > Mezipaměť prostředí Azure HPC se dá vytvořit programově, a to prostřednictvím [šablony Azure Resource Manager](../azure-resource-manager/templates/overview.md) nebo přímým přístupem k jejímu rozhraní API. Podrobnosti získáte od týmu Azure HPC cache.

## <a name="failover-example"></a>Příklad převzetí služeb při selhání

Představte si například, že chcete najít mezipaměť prostředí Azure HPC v Východní USA oblasti Azure. Bude mít přístup k datům uloženým v místním datovém centru.

Jako zálohu při selhání můžete použít mezipaměť v Západní USA 2 oblasti.

Při vytváření mezipaměti v Východní USA Připravte druhou mezipaměť pro nasazení v Západní USA 2. Tuto přípravu můžete automatizovat pomocí skriptování nebo šablon.

V případě selhání oblasti v rámci Východní USA vytvořte mezipaměť, kterou jste připravili v Západní USA 2 oblasti.

Po vytvoření mezipaměti přidejte cíle úložiště, které odkazují na stejné místní úložiště dat, a použijte stejné agregované cesty oboru názvů jako cíle úložiště staré mezipaměti.

Pokud jsou původní klienti ovlivněni, vytvořte nové klienty v oblasti Západní USA 2 pro použití s novou mezipamětí.

Všichni klienti budou muset připojit novou mezipaměť, a to i v případě, že nedošlo k ovlivnění těchto oblastí klienty. Nová mezipaměť má jiné adresy připojení než staré.

## <a name="learn-more"></a>Další informace

Průvodce architekturou aplikací Azure obsahuje další informace o tom, jak provést [obnovení při přerušení služeb v rámci oblasti](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>).
