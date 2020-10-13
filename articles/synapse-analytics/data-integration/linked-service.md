---
title: Zabezpečení propojené služby
description: Naučte se zřizovat a zabezpečovat propojenou službu pomocí spravované virtuální sítě.
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: f5a3c73d60f038820de100f99c554eec27fd6f55
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90033179"
---
# <a name="secure-a-linked-service-with-private-links"></a>Zabezpečení propojené služby pomocí privátních odkazů 

V tomto článku se dozvíte, jak zabezpečit propojenou službu v synapse pomocí privátního koncového bodu.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet Azure Storage**: Azure Data Lake Gen 2 použijete jako *zdrojové* úložiště dat. Pokud nemáte účet úložiště, přečtěte si článek [vytvoření Azure Storage účtu](../../storage/blobs/data-lake-storage-quickstart-create-account.md) , kde najdete kroky pro jeho vytvoření. Ujistěte se, že účet úložiště má k přístupu přístup synapse Studio IP a že povolujete přístup jenom **vybraným sítím** k účtu úložiště. Nastavení v okně **brány firewall a virtuální sítě** by mělo vypadat jako na obrázku níže.

![Zabezpečený účet úložiště](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Vytvoření propojené služby s privátními odkazy

V Azure synapse Analytics je propojená služba, kde můžete definovat informace o připojení k ostatním službám. V této části přidáte Azure synapse Analytics a Azure Data Lake Gen 2 jako propojené služby.

1. Otevřete Azure synapse Studio a pokračujte na kartu **Spravovat** .
1. V části **externí připojení**vyberte **propojené služby**.
1. Chcete-li přidat propojenou službu, vyberte možnost **Nový**.
1. V seznamu Vyberte dlaždici Azure Data Lake Storage Gen2 a vyberte **pokračovat**.
1. Ujistěte se, že jste povolili **interaktivní vytváření**. Povolení může trvat přibližně 1 minutu. 
1. Zadejte přihlašovací údaje pro ověření. Typy ověřování aktuálně podporují klíč účtu, instanční objekt a spravovanou identitu. Vyberte test připojení a ověřte správnost vašich přihlašovacích údajů.
1. Vyberte **Test připojení**, nemělo by se zdařit, protože účet úložiště nepovoluje přístup k němu bez vytvoření a schválení privátního koncového bodu. V chybové zprávě byste měli vidět odkaz pro vytvoření **privátního koncového bodu** , pomocí kterého můžete přejít k další části. Pokud tento odkaz provedete, přeskočte další část.
1. Po dokončení vyberte **Vytvořit**.

## <a name="create-a-managed-private-endpoint"></a>Vytvoření spravovaného privátního koncového bodu

Pokud jste nevybrali možnost hypertextový odkaz při testování výše uvedeného připojení, použijte následující cestu. Vytvořte spravovaný privátní koncový bod, ke kterému se připojíte přes propojenou službu vytvořenou výše.

1. Přejít na kartu **Spravovat** .
1. Přejít do části **spravované virtuální sítě** .
1. V části spravovaný privátní koncový bod vyberte **+ Nový** .
1. V seznamu Vyberte dlaždici Azure Data Lake Storage Gen2 a vyberte **pokračovat**.
1. Zadejte název účtu úložiště, který jste vytvořili výše.
1. Vyberte **Vytvořit**.
1. Měli byste vidět, že se po chvíli čekání na vytvoření privátního odkazu vyžaduje schválení.

## <a name="private-link-approval"></a>Schválení privátního odkazu
1. Vyberte privátní koncový bod, který jste vytvořili výše. Můžete se podívat na hypertextový odkaz, který vám umožní schválit privátní koncový bod na úrovni účtu úložiště. *Alternativou je přejít přímo na účet úložiště Azure Portal a přejít do okna **připojení privátního koncového bodu** .*
1. Zaznačte si privátní koncový bod, který jste vytvořili v nástroji Studio, a vyberte **schválit**.
1. Přidejte popis a vyberte **Ano** .
1. Vraťte se do synapse studia v části **spravované virtuální sítě** na kartě **Spravovat** .
1. Může trvat přibližně 1 minutu, aby se schválení projevilo pro váš soukromý koncový bod.

## <a name="check-the-connection-works"></a>Ověřte, že připojení funguje
1. Přejít na kartu **Spravovat** a vybrat propojenou službu, kterou jste vytvořili.
1. Ujistěte se, že je **interaktivní vytváření obsahu** aktivní.
1. Vyberte **Test připojení**. Mělo by se zobrazit úspěšné připojení.

Nyní jste navázali zabezpečené a privátní připojení mezi synapse a propojenou službou.

## <a name="next-steps"></a>Další kroky

Další porozumění spravovanému privátnímu koncovému bodu v synapse Analytics najdete v článku o [konceptu o synapse spravovaném privátním koncovým bodem](data-integration-data-lake.md) .

Další informace o integraci dat pro synapse Analytics najdete v článku ingestování [dat do data Lake](data-integration-data-lake.md) .
