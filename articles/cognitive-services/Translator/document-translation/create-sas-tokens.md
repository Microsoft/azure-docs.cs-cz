---
title: Vytvoření tokenu SAS (Shared Access Signature) pro kontejnery a objekty BLOB pomocí Microsoft Průzkumník služby Storage
description: Postup vytvoření sdíleného přístupového tokenu (SAS) pro kontejnery a objekty BLOB pomocí Microsoft Průzkumník služby Storage a Azure Portal
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 03/05/2021
ms.openlocfilehash: e40fc569ad1c8ec5894f06915422bea37cfc40ee
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489629"
---
# <a name="create-sas-tokens-for-document-translation-processing"></a>Vytváření tokenů SAS pro zpracování překladu dokumentu

V tomto článku se dozvíte, jak vytvořit tokeny sdíleného přístupového podpisu (SAS) pomocí Průzkumník služby Azure Storage nebo Azure Portal. Token SAS poskytuje zabezpečený a delegovaný přístup k prostředkům ve vašem účtu úložiště Azure.

## <a name="create-your-sas-tokens-with-azure-storage-explorer"></a>Vytvoření tokenů SAS pomocí Průzkumník služby Azure Storage

### <a name="prerequisites"></a>Požadavky

* Budete potřebovat aplikaci [**Průzkumník služby Azure Storage**](../../../vs-azure-tools-storage-manage-with-storage-explorer.md) nainstalovanou ve vývojovém prostředí Windows, MacOS nebo Linux. Průzkumník služby Azure Storage je bezplatný nástroj, který umožňuje snadnou správu prostředků cloudového úložiště Azure.
* Po instalaci aplikace Průzkumník služby Azure Storage [připojte ji k účtu úložiště](../../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#connect-to-a-storage-account-or-service) , který používáte pro překlad dokumentu.

### <a name="create-your-tokens"></a>Vytvoření tokenů

### <a name="sas-tokens-for-containers"></a>[Tokeny SAS pro kontejnery](#tab/Containers)

1. Na místním počítači otevřete aplikaci Průzkumník služby Azure Storage a přejděte do připojených **účtů úložiště**.
1. Rozbalte uzel účty úložiště a vyberte **kontejnery objektů BLOB**.
1. Rozbalte uzel kontejnery objektů BLOB a klikněte pravým tlačítkem na uzel **kontejneru** úložiště nebo zobrazte nabídku Možnosti.
1. V nabídce Možnosti vyberte **získat sdílený přístupový podpis...** .
1. V okně **sdílený přístupový podpis** proveďte následující výběry:
    * Vyberte **zásady přístupu** (výchozí nastavení není žádné).
    * Zadejte datum a čas **podepsaného klíče a datum a čas** **vypršení platnosti** . Doporučuje se, protože po vygenerování se SAS nedá odvolat.
    * Vyberte **časové pásmo** pro datum a čas začátku a konce platnosti (výchozí je místní).
    * Určete **oprávnění** kontejneru zaškrtnutím nebo zrušením příslušného zaškrtávacího políčka.
    * Zkontrolujte a vyberte **vytvořit**.

1. Nové okno se zobrazí s názvem **kontejneru** , **identifikátorem URI** a **řetězcem dotazu** pro váš kontejner.  
1. **Zkopírujte a vložte hodnoty kontejneru, identifikátor URI a řetězce dotazu do zabezpečeného umístění. Budou zobrazeny pouze jednou a nelze je načíst, jakmile je okno zavřeno.**
1. Pokud chcete vytvořit adresu URL SAS, přidejte token SAS (URI) k adrese URL služby úložiště.

### <a name="sas-tokens-for-blobs"></a>[Tokeny SAS pro objekty blob](#tab/blobs)

1. Na místním počítači otevřete aplikaci Průzkumník služby Azure Storage a přejděte do připojených **účtů úložiště**.
1. Rozbalte uzel úložiště a vyberte **kontejnery objektů BLOB**.
1. Rozbalte uzel kontejnery objektů BLOB a vyberte uzel **kontejneru** pro zobrazení obsahu v hlavním okně.
1. Vyberte objekt blob, pro který chcete delegovat přístup SAS, a kliknutím pravým tlačítkem zobrazíte nabídku Možnosti.
1. V nabídce Možnosti vyberte **získat sdílený přístupový podpis...** .
1. V okně **sdílený přístupový podpis** proveďte následující výběry:
    * Vyberte **zásady přístupu** (výchozí nastavení není žádné).
    * Zadejte datum a čas **podepsaného klíče a datum a čas** **vypršení platnosti** . Doporučuje se, protože po vygenerování se SAS nedá odvolat.
    * Vyberte **časové pásmo** pro datum a čas začátku a konce platnosti (výchozí je místní).
    * Určete **oprávnění** kontejneru zaškrtnutím nebo zrušením příslušného zaškrtávacího políčka.
    * Zkontrolujte a vyberte **vytvořit**.
1. Nové okno se zobrazí s názvem **objektu BLOB** , **identifikátorem URI** a **řetězcem dotazu** pro váš objekt BLOB.  
1. **Zkopírujte a vložte hodnoty řetězce blob, URI a Query do zabezpečeného umístění. Budou zobrazeny pouze jednou a nelze je načíst, jakmile je okno zavřeno.**
1. Pokud chcete vytvořit adresu URL SAS, přidejte token SAS (URI) k adrese URL služby úložiště.

---

## <a name="create-sas-tokens-for-blobs-in-the-azure-portal"></a>Vytváření tokenů SAS pro objekty BLOB v Azure Portal

> [!NOTE]
> Vytváření tokenů SAS pro kontejnery přímo v Azure Portal není aktuálně podporováno. Můžete ale vytvořit token SAS pomocí [**Průzkumník služby Azure Storage**](#create-your-sas-tokens-with-azure-storage-explorer) nebo dokončit úlohu [programově](../../../storage/blobs/sas-service-create.md).

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

* Aktivní [**účet Azure**](https://azure.microsoft.com/free/cognitive-services/).  Pokud ho nemáte, můžete si [**vytvořit bezplatný účet**](https://azure.microsoft.com/free/).
* Prostředek služby [**Translator**](https://ms.portal.azure.com/#create/Microsoft) **(nejedná se** o Cognitive Services prostředek s více službami.  *Viz* [Vytvoření nového prostředku Azure](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource).  
* [**Účet úložiště objektů BLOB v Azure**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Vytvoříte kontejnery pro ukládání a uspořádání dat objektů BLOB v rámci svého účtu úložiště.

### <a name="create-your-tokens"></a>Vytvoření tokenů

Přejděte na [Azure Portal](https://ms.portal.azure.com/#home) a přejděte následujícím způsobem:  

 **Váš účet úložiště** → **Containers** → **Váš kontejner** → váš **objekt BLOB**

1. V nabídce v horní části stránky vyberte **Generovat SAS** .

1. Vyberte **podepisující metoda** → **klíč delegování uživatelů**.

1. Definujte **oprávnění** zaškrtnutím nebo zrušením příslušného zaškrtávacího políčka.

1. Zadejte časy **začátku** a **konce platnosti** podepsaného klíče.

1. Pole **povolených IP adres** je volitelné a určuje IP adresu nebo rozsah IP adres, ze kterých se mají přijímat žádosti. Pokud se IP adresa požadavku neshoduje s IP adresou nebo rozsahem adres zadaným na tokenu SAS, nebude se autorizovat.

1. Pole **Povolené protokoly** je volitelné a určuje protokol povolený pro žádost vytvořenou pomocí SAS. Výchozí hodnota je HTTPS.

1. Zkontrolujte a pak vyberte **Generovat token SAS a adresu URL**.

1. V dolní části okna se zobrazí řetězec dotazu na **tokeny SAS objektu BLOB** a **Adresa URL SAS objektu BLOB** .  

1. **Zkopírujte a vložte hodnoty tokenu SAS objektu BLOB a adresy URL do zabezpečeného umístění. Budou zobrazeny pouze jednou a nelze je načíst, jakmile je okno zavřeno.**

1. Pokud chcete vytvořit adresu URL SAS, přidejte token SAS (URI) k adrese URL služby úložiště.

## <a name="learn-more"></a>Další informace

* [Vytváření tokenů SAS pro objekty blob nebo kontejnery prostřednictvím kódu programu](../../../storage/blobs/sas-service-create.md)
* [Oprávnění pro adresář, kontejner nebo objekt BLOB](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s překladem dokumentů](get-started-with-document-translation.md)
>
>