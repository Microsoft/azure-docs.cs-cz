---
title: Šifrování v Azure Data Lake Storage Gen1 | Microsoft Docs
description: Šifrování v Azure Data Lake Storage Gen1 pomáhá chránit vaše data, implementovat zásady podnikového zabezpečení a splnit požadavky na dodržování legislativních předpisů. Tento článek poskytuje přehled návrhu a popisuje některé technické aspekty implementace.
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: f924cb7462f7f8c9939ec261b7ef200ceb8ea70b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92109149"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Šifrování dat v Azure Data Lake Storage Gen1

Šifrování v Azure Data Lake Storage Gen1 pomáhá chránit vaše data, implementovat zásady podnikového zabezpečení a splnit požadavky na dodržování legislativních předpisů. Tento článek poskytuje přehled návrhu a popisuje některé technické aspekty implementace.

Data Lake Storage Gen1 podporuje šifrování dat v klidovém režimu i při přenosu. V případě neaktivních dat Data Lake Storage Gen1 podporuje ve výchozím nastavení "transparentní šifrování". Tady je podrobnější vysvětlení významu těchto termínů:

* **Ve výchozím** nastavení: Když vytváříte nový účet Data Lake Storage Gen1, výchozí nastavení povolí šifrování. Data, která jsou uložená v Data Lake Storage Gen1, se pak před uložením na trvalé médium vždycky šifrují. Toto chování platí pro veškerá data a po vytvoření účtu nejde změnit.
* **Transparentní**: Data Lake Storage Gen1 automaticky šifruje data před jejich uložením a dešifruje data před jejich načtením. Šifrování je nakonfigurované a spravované na úrovni účtu Data Lake Storage Gen1 správcem. Rozhraní API pro přístup k datům se nemění. Proto se v aplikacích a službách, které komunikují s Data Lake Storage Gen1 v důsledku šifrování, nevyžadují žádné změny.

Data v přenosu (označovaná také jako data v pohybu) se také vždy šifrují v Data Lake Storage Gen1. Kromě šifrování dat před uložením na trvalé médium se také vždy šifrují přenášená data pomocí protokolu HTTPS. Protokol HTTPS je jediným protokolem, který je podporován pro rozhraní Data Lake Storage Gen1 REST. Následující diagram znázorňuje, jak se data zašifrují v Data Lake Storage Gen1:

![Diagram šifrování dat v Data Lake Storage Gen1](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Nastavení šifrování pomocí Data Lake Storage Gen1

Šifrování pro Data Lake Storage Gen1 je nastaveno při vytváření účtu a ve výchozím nastavení je vždy povoleno. Klíče můžete buď spravovat sami, nebo můžete Data Lake Storage Gen1 jim dovolit, aby je spravovali (Toto je výchozí nastavení).

Další informace najdete v tématu [Začínáme](./data-lake-store-get-started-portal.md).

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Jak šifrování funguje v Data Lake Storage Gen1

Následující informace zahrnují způsob správy hlavních šifrovacích klíčů a vysvětluje tři různé typy klíčů, které můžete použít v šifrování dat pro Data Lake Storage Gen1.

### <a name="master-encryption-keys"></a>Hlavní šifrovací klíče

Data Lake Storage Gen1 poskytuje dva režimy pro správu hlavních šifrovacích klíčů (hlavních šifrovacích klíčů). Prozatím předpokládejme, že hlavní šifrovací klíč je klíč nejvyšší úrovně. K dešifrování jakýchkoli dat uložených v Data Lake Storage Gen1 se vyžaduje přístup k hlavnímu šifrovacímu klíči.

Pro správu hlavních šifrovacích klíčů existují tyto dva režimy:

*   Klíče spravované službou
*   Klíče spravované zákazníkem

V obou režimech je hlavní šifrovací klíč zabezpečen uložením ve službě Azure Key Vault. Key Vault je plně spravovaná, vysoce zabezpečená služba v Azure, kterou můžete použít k bezpečnému ukládání kryptografických klíčů. Další informace najdete v tématu [Key Vault](https://azure.microsoft.com/services/key-vault).

Tady je stručné porovnání možností, které nabízí dva režimy správy hlavních šifrovacích klíčů.

| Otázka | Klíče spravované službou | Klíče spravované zákazníkem |
| -------- | -------------------- | --------------------- |
|Jak se data ukládají?|Před uložením se vždy šifrují.|Před uložením se vždy šifrují.|
|Kde je uložený hlavní šifrovací klíč?|Key Vault|Key Vault|
|Jsou nějaké šifrovací klíče uložené v nezašifrované podobě mimo službu Key Vault? |No|No|
|Může služba Key Vault načíst hlavní šifrovací klíč?|No. Po uložení hlavního šifrovacího klíče ve službě Key Vault ho lze použít pouze k šifrování a dešifrování.|No. Po uložení hlavního šifrovacího klíče ve službě Key Vault ho lze použít pouze k šifrování a dešifrování.|
|Kdo je vlastníkem instance služby Key Vault a hlavního šifrovacího klíče?|Služba Data Lake Storage Gen1|Vlastníte instanci služby Key Vault, která patří do vašeho předplatného Azure. Hlavní šifrovací klíč ve službě Key Vault může být spravovaný softwarem nebo hardwarem.|
|Můžete odvolat přístup k hlavní šifrovací klíč pro službu Data Lake Storage Gen1?|No|Yes. Můžete spravovat seznamy řízení přístupu v Key Vault a odebrat položky řízení přístupu k identitě služby Data Lake Storage Gen1.|
|Je možné trvale odstranit hlavní šifrovací klíč?|No|Yes. Odstraníte-li hlavní šifrovací klíč z Key Vault, nelze data v Data Lake Storage Gen1m účtu dešifrovat nikdo, včetně služby Data Lake Storage Gen1. <br><br> Pokud jste hlavní šifrovací klíč před odstraněním ze služby Key Vault explicitně zazálohovali, je možné ho obnovit a následně obnovit i data. Pokud jste ale hlavní šifrovací klíč ještě před odstraněním z Key Vault nezálohovali, data v Data Lake Storage Gen1m účtu už nebude možné dešifrovat.|


Kromě tohoto rozdílu, tedy kdo spravuje hlavní šifrovací klíče a instanci služby Key Vault, ve které se nachází, je zbytek návrhu pro oba režimy stejný.

Při výběru režimu pro hlavní šifrovací klíče je důležité pamatovat na následující:

*   Když zřizujete účet Data Lake Storage Gen1, můžete si vybrat, jestli se mají používat spravované klíče zákazníka nebo spravované klíče služby.
*   Po zřízení účtu Data Lake Storage Gen1 nelze změnit režim.

### <a name="encryption-and-decryption-of-data"></a>Šifrování a dešifrování dat

V návrhu šifrování dat používají tři typy klíčů. Následující tabulka poskytuje souhrn:

| Klíč                   | Zkratka | Přidružený k | Umístění úložiště                             | Typ       | Poznámky                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Hlavní šifrovací klíč | MEK          | Účet Data Lake Storage Gen1 | Key Vault                              | Asymetrický | Dá se spravovat pomocí Data Lake Storage Gen1 nebo.                                                              |
| Šifrovací klíč dat   | DEK          | Účet Data Lake Storage Gen1 | Trvalé úložiště spravované službou Data Lake Storage Gen1 | Symetrický  | Klíč DEK je šifrovaný klíčem MEK. Na trvalé médium se ukládá šifrovaný klíč DEK. |
| Šifrovací klíč bloku  | BEK          | Blokem dat | Žádné                                         | Symetrický  | Klíč BEK je odvozený od klíče DEK a příslušného datového bloku.                                                      |

Následující diagram znázorňuje tyto koncepty:

![Klíče v šifrování dat](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo algoritmus dešifrování souboru:
1.  Ověřte, zda je klíč DEK pro účet Data Lake Storage Gen1 uložen do mezipaměti a připraven k použití.
    - Pokud není, přečtení šifrovaného klíče DEK z trvalého úložiště a jeho odeslání do služby Key Vault k dešifrování. Uložení dešifrovaného klíče DEK v mezipaměti. Nyní je připraven k použití.
2.  Pro každý blok dat v souboru:
    - Načtení šifrovaného bloku dat z trvalého úložiště.
    - Vygenerování klíče BEK z klíče DEK a šifrovaného bloku dat.
    - Dešifrování dat pomocí klíče BEK.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo algoritmus šifrování bloku dat:
1.  Ověřte, zda je klíč DEK pro účet Data Lake Storage Gen1 uložen do mezipaměti a připraven k použití.
    - Pokud není, přečtení šifrovaného klíče DEK z trvalého úložiště a jeho odeslání do služby Key Vault k dešifrování. Uložení dešifrovaného klíče DEK v mezipaměti. Nyní je připraven k použití.
2.  Vygenerování jedinečného klíče BEK pro blok dat z klíče DEK.
3.  Šifrování datového bloku pomocí klíče BEK s použitím šifrování AES-256.
4.  Uložení šifrovaného datového bloku v trvalém úložišti.

> [!NOTE] 
> Klíč DEK je vždy uložený zašifrovaný klíčem MEK, ať už se jedná o trvalé médium nebo mezipaměť.

## <a name="key-rotation"></a>Obměna klíčů

Pokud používáte klíče spravované zákazníkem, můžete obměňovat klíč MEK. Informace o tom, jak nastavit účet Data Lake Storage Gen1 pomocí klíčů spravovaných zákazníkem, najdete v tématu [Začínáme](./data-lake-store-get-started-portal.md).

### <a name="prerequisites"></a>Předpoklady

Při nastavování účtu Data Lake Storage Gen1 jste se rozhodli používat vlastní klíče. Tuto možnost po vytvoření účtu nejde změnit. Následující postup předpokládá, že používáte klíče spravované zákazníkem (tedy že jste zvolili vlastní klíče ze služby Key Vault).

Všimněte si, že pokud použijete výchozí možnosti šifrování, vaše data se vždycky šifrují pomocí klíčů spravovaných pomocí Data Lake Storage Gen1. V této možnosti není možné otáčet klíče, protože jsou spravované pomocí Data Lake Storage Gen1.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>Jak otočit hlavní šifrovací klíč v Data Lake Storage Gen1

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Přejděte do instance Key Vault, kde jsou uložené vaše klíče přidružené k vašemu účtu Data Lake Storage Gen1. Vyberte **Klíče**.

    ![Snímek obrazovky služby Key Vault](./media/data-lake-store-encryption/keyvault.png)

3. Vyberte klíč přidružený k vašemu Data Lake Storage Gen1 účtu a vytvořte novou verzi tohoto klíče. Všimněte si, že Data Lake Storage Gen1 aktuálně podporuje pouze rotaci klíčů pro novou verzi klíče. Obměnu za jiný klíč nepodporuje.

   ![Snímek obrazovky okna Klíče se zvýrazněnou možností Nová verze](./media/data-lake-store-encryption/keynewversion.png)

4. Přejděte na účet Data Lake Storage Gen1 a vyberte **šifrování**.

   ![Snímek obrazovky okna Data Lake Storage Gen1 účtu se zvýrazněným šifrováním](./media/data-lake-store-encryption/select-encryption.png)

5. Zobrazí se zpráva informující o dostupnosti nové verze klíče. Kliknutím na **Obměnit klíč** aktualizujte klíč na novou verzi.

   ![Snímek obrazovky okna Data Lake Storage Gen1 se zvýrazněnou zprávou a otočením klávesy](./media/data-lake-store-encryption/rotatekey.png)

Tato operace by neměla trvat déle než dvě minuty a nemělo by dojít k žádnému výpadku v důsledku obměny klíče. Po dokončení operace se začne používat nová verze klíče.

> [!IMPORTANT]
> Po dokončení operace obměny klíče se už k šifrování vašich dat nebude aktivně používat stará verze klíče.  Ve výjimečných případech neočekávaného selhání, které ovlivní dokonce i redundantní kopie vašich dat, se však můžou data obnovit ze zálohy, která stále používá starý klíč. Pokud chcete zajistit dostupnost dat i v těchto výjimečných případech, uchovávejte kopii předchozí verze šifrovacího klíče. Osvědčené postupy pro plánování zotavení po havárii najdete v tématu [pokyny pro zotavení po havárii pro data v Data Lake Storage Gen1](data-lake-store-disaster-recovery-guidance.md) .