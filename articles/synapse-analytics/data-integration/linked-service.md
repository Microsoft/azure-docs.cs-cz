---
title: Poskytování a zabezpečení propojené služby v Azure Synapse Analytics
description: Zjistěte, jak zřídit a zabezpečit propojenou službu pomocí spravované virtuální sítě
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 435c3fd6b1e6444fa3a31c68b4d74c2553d2e634
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430549"
---
# <a name="securing-a-linked-service-with-private-links"></a>Zabezpečení propojené služby pomocí soukromých odkazů 

V tomto článku se dozvíte, jak zabezpečit propojené služby v Synapse s privátní koncový bod.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**: Pokud nemáte předplatné Azure, vytvořte [si bezplatný účet Azure,](https://azure.microsoft.com/free/) než začnete.
* **Účet Azure Storage**: Azure Data Lake Gen 2 používáte jako *zdrojové* úložiště dat. Pokud nemáte účet úložiště, najdete v [tématu vytvoření účtu Azure Storage](../../storage/blobs/data-lake-storage-quickstart-create-account.md) pro kroky k jeho vytvoření. Ujistěte se, že účet úložiště obsahuje filtrování IP adres synapse studio pro přístup k němu a že povolíte přístup k účtu úložiště pouze **vybraným sítím.** Nastavení pod okovou **firewallem a virtuálními sítěmi** by mělo vypadat jako obrázek níže.

![Účet zabezpečeného úložiště](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Vytvoření propojené služby pomocí soukromých odkazů

V Azure Synapse Analytics, propojené služby je místo, kde definujete informace o připojení k jiným službám. V této části přidáte Azure Synapse Analytics a Azure Data Lake Gen 2 jako propojené služby.

1. Otevřete Azure Synapse Studio a přejděte na kartu **Spravovat.**
1. V části **Externí připojení**vyberte **Propojené služby**.
1. Chcete-li přidat propojenou službu, klepněte na tlačítko **Nový**.
1. Vyberte dlaždici Azure Data Lake Storage Gen2 ze seznamu a klikněte na **Pokračovat**.
1. Ujistěte se, že jste **povolili interaktivní vytváření**. Povolení může trvat přibližně 1 minutu. 
1. Zadejte ověřovací pověření. Klíč účtu, instanční objekt a spravovaná identita jsou aktuálně podporované typy ověřování. Chcete-li ověřit správnost pověření, klepněte na tlačítko Testovat připojení.
1. Vyberte **Testovat připojení**, mělo by selhat, protože účet úložiště neumožňuje přístup k němu bez vytvoření a schválení privátní koncový bod. V chybové zprávě byste měli vidět odkaz na vytvoření **soukromého koncového bodu,** který můžete sledovat přejít na další část. Pokud budete postupovat podle tohoto odkazu, přeskočte další část.
1. Po dokončení vyberte **Vytvořit**.

## <a name="create-a-managed-private-endpoint"></a>Vytvoření spravovaného privátního koncového bodu

V případě, že jste neklikali do hypertextového odkazu při testování výše uvedeného připojení, postupujte podle následující cesty. Nyní je třeba vytvořit spravovaný soukromý koncový bod, který se připojíte k propojené službě vytvořené výše.

1. Přejděte na kartu **Spravovat.**
1. Přejděte do části **Spravované virtuální sítě.**
1. V části Spravované soukromé koncového bodu vyberte **+ Nový.**
1. Vyberte dlaždici Azure Data Lake Storage Gen2 ze seznamu a vyberte **Pokračovat**.
1. Zadejte název účtu úložiště, který jste vytvořili výše.
1. Vybrat **vytvořit**
1. Měli byste vidět po čekání několik sekund, že soukromý odkaz vytvořil potřebuje schválení.

## <a name="approval-of-a-private-link"></a>Schválení soukromého spojení
1. Vyberte soukromý koncový bod, který jste vytvořili výše. Můžete zobrazit hypertextový odkaz, který vám umožní schválit soukromý koncový bod na úrovni účtu úložiště. *Alternativou je přejít přímo na účet úložiště portálu Azure a přejít do okna **privátní koncového bodu.***
1. Zaškrtněte soukromý koncový bod, který jste vytvořili ve studiu, a vyberte **schválit**.
1. Přidejte popis a klikněte na **tlačítko ano.**
1. Vraťte se do Synapse Studia v části **Spravované virtuální sítě** na kartě **Správa.**
1. Mělo by trvat přibližně 1 minutu, než se schválení projeví pro váš soukromý koncový bod.

## <a name="check-the-connection-works"></a>Zkontrolujte, zda připojení funguje
1. Přejděte na kartu **Spravovat** a vyberte propojenou službu, kterou jste vytvořili.
1. Ujistěte se, že interaktivní **vytváření** je aktivní.
1. Vyberte **Test připojení**. Měli byste vidět připojení být úspěšné.

Nyní jste vytvořili bezpečné a soukromé spojení mezi Synapse a vaší propojené služby!

## <a name="next-steps"></a>Další kroky

Pokud chcete v Synapse Analytics rozvíjet další porozumění spravovanému privátnímu koncovému bodu, přečtěte si [článek Koncept kolem soukromého koncového bodu spravovaného synapse.](data-integration-data-lake.md)

Další informace o integraci dat pro Synapse Analytics najdete v tématu [ingestování dat do](data-integration-data-lake.md) článku data lake.