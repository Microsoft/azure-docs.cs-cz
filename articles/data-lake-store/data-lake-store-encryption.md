---
title: Šifrování v úložišti datových jezer Azure Gen1 | Dokumenty společnosti Microsoft
description: Šifrování v Azure Data Lake Storage Gen1 vám pomůže chránit vaše data, implementovat podnikové zásady zabezpečení a splnit požadavky na dodržování předpisů. Tento článek poskytuje přehled návrhu a popisuje některé technické aspekty implementace.
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: a009f212bd8baaa353d602dc6090aeeccddd4936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878363"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Šifrování dat v Azure Data Lake Storage Gen1

Šifrování v Azure Data Lake Storage Gen1 vám pomůže chránit vaše data, implementovat podnikové zásady zabezpečení a splnit požadavky na dodržování předpisů. Tento článek poskytuje přehled návrhu a popisuje některé technické aspekty implementace.

Data Lake Storage Gen1 podporuje šifrování dat v klidovém stavu i při přenosu. Pro data v klidovém stavu, Data Lake Storage Gen1 podporuje "on ve výchozím nastavení", transparentní šifrování. Tady je podrobnější vysvětlení významu těchto termínů:

* **Zapnuto ve výchozím nastavení**: Když vytvoříte nový účet Data Lake Storage Gen1, výchozí nastavení povolí šifrování. Poté jsou data uložená v zařízení Data Lake Storage Gen1 vždy šifrována před uložením na trvalá média. Toto chování platí pro veškerá data a po vytvoření účtu nejde změnit.
* **Transparentní**: Data Lake Storage Gen1 automaticky šifruje data před uchováním a dešifruje data před načtením. Šifrování je nakonfigurováno a spravováno na úrovni účtu Data Lake Storage Gen1 správcem. Rozhraní API pro přístup k datům se nemění. Proto jsou vyžadovány žádné změny v aplikacích a službách, které interagují s Data Lake Storage Gen1 z důvodu šifrování.

Data při přenosu (také známá jako data v pohybu) jsou také vždy šifrována v data Lake Storage Gen1. Kromě šifrování dat před uložením na trvalé médium se také vždy šifrují přenášená data pomocí protokolu HTTPS. HTTPS je jediný protokol, který je podporován pro rozhraní Storage Data Lake Gen1 REST. Následující diagram znázorňuje, jak se data zašifrují v úložišti datového jezera Gen1:

![Diagram šifrování dat v úložišti datového jezera Gen1](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Nastavení šifrování pomocí funkce Data Lake Storage Gen1

Šifrování pro Data Lake Storage Gen1 je nastaveno během vytváření účtu a je ve výchozím nastavení vždy povoleno. Klíče můžete spravovat sami nebo povolit, aby je data Lake Storage Gen1 spravovaly za vás (toto je výchozí nastavení).

Další informace najdete v tématu [Začínáme](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Jak funguje šifrování v úložišti datového jezera Gen1

Následující informace se týkají správy hlavních šifrovacích klíčů a vysvětlují tři různé typy klíčů, které můžete použít při šifrování dat pro data Lake Storage Gen1.

### <a name="master-encryption-keys"></a>Hlavní šifrovací klíče

Data Lake Storage Gen1 poskytuje dva režimy pro správu hlavních šifrovacích klíčů (MEKs). Prozatím předpokládejme, že hlavní šifrovací klíč je klíč nejvyšší úrovně. Přístup k hlavnímu šifrovacímu klíči je nutný k dešifrování všech dat uložených v zařízení Data Lake Storage Gen1.

Pro správu hlavních šifrovacích klíčů existují tyto dva režimy:

*   Klíče spravované službou
*   Klíče spravované zákazníkem

V obou režimech je hlavní šifrovací klíč zabezpečen uložením ve službě Azure Key Vault. Key Vault je plně spravovaná, vysoce zabezpečená služba v Azure, kterou můžete použít k bezpečnému ukládání kryptografických klíčů. Další informace najdete v tématu [Key Vault](https://azure.microsoft.com/services/key-vault).

Tady je stručné porovnání možností, které nabízí dva režimy správy hlavních šifrovacích klíčů.

|  | Klíče spravované službou | Klíče spravované zákazníkem |
| --- | --- | --- |
|Jak se data ukládají?|Před uložením se vždy šifrují.|Před uložením se vždy šifrují.|
|Kde je uložený hlavní šifrovací klíč?|Key Vault|Key Vault|
|Jsou nějaké šifrovací klíče uložené v nezašifrované podobě mimo službu Key Vault? |Ne|Ne|
|Může služba Key Vault načíst hlavní šifrovací klíč?|Ne. Po uložení hlavního šifrovacího klíče ve službě Key Vault ho lze použít pouze k šifrování a dešifrování.|Ne. Po uložení hlavního šifrovacího klíče ve službě Key Vault ho lze použít pouze k šifrování a dešifrování.|
|Kdo je vlastníkem instance služby Key Vault a hlavního šifrovacího klíče?|Služba Data Lake Storage Gen1|Vlastníte instanci služby Key Vault, která patří do vašeho předplatného Azure. Hlavní šifrovací klíč ve službě Key Vault může být spravovaný softwarem nebo hardwarem.|
|Můžete odvolat přístup k MEK pro službu Data Lake Storage Gen1?|Ne|Ano. Seznamy řízení přístupu můžete spravovat v trezoru klíčů a odebrat položky řízení přístupu k identitě služby pro službu Data Lake Storage Gen1.|
|Je možné trvale odstranit hlavní šifrovací klíč?|Ne|Ano. Pokud odstraníte MEK z trezoru klíčů, data v účtu Data Lake Storage Gen1 nemůže dešifrovat nikdo, včetně služby Data Lake Storage Gen1. <br><br> Pokud jste hlavní šifrovací klíč před odstraněním ze služby Key Vault explicitně zazálohovali, je možné ho obnovit a následně obnovit i data. Pokud jste však mek nezálohovali před jeho odstraněním z trezoru klíčů, data v účtu Gen1 úložiště datového jezera nelze poté nikdy dešifrovat.|


Kromě tohoto rozdílu, tedy kdo spravuje hlavní šifrovací klíče a instanci služby Key Vault, ve které se nachází, je zbytek návrhu pro oba režimy stejný.

Při výběru režimu pro hlavní šifrovací klíče je důležité pamatovat na následující:

*   Můžete si vybrat, zda chcete používat klíče spravované zákazníkem nebo klíče spravované službou při zřizování účtu Data Lake Storage Gen1.
*   Po zřízení účtu Data Lake Storage Gen1 nelze změnit režim.

### <a name="encryption-and-decryption-of-data"></a>Šifrování a dešifrování dat

V návrhu šifrování dat používají tři typy klíčů. Následující tabulka poskytuje souhrn:

| Klíč                   | Zkratka | Přidružený k | Umístění úložiště                             | Typ       | Poznámky                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Hlavní šifrovací klíč | MEK          | Účet Gen1 úložiště datového jezera | Key Vault                              | Asymetrický | Může být spravován společností Data Lake Storage Gen1 nebo vámi.                                                              |
| Šifrovací klíč dat   | DEK          | Účet Gen1 úložiště datového jezera | Trvalé úložiště spravované službou Data Lake Storage Gen1 | Symetrický  | Klíč DEK je šifrovaný klíčem MEK. Na trvalé médium se ukládá šifrovaný klíč DEK. |
| Šifrovací klíč bloku  | BEK          | Blokem dat | Žádný                                         | Symetrický  | Klíč BEK je odvozený od klíče DEK a příslušného datového bloku.                                                      |

Následující diagram znázorňuje tyto koncepty:

![Klíče v šifrování dat](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo algoritmus dešifrování souboru:
1.  Zkontrolujte, jestli dek pro účet Data Lake Storage Gen1 je uložena do mezipaměti a připravenko k použití.
    - Pokud není, přečtení šifrovaného klíče DEK z trvalého úložiště a jeho odeslání do služby Key Vault k dešifrování. Uložení dešifrovaného klíče DEK v mezipaměti. Nyní je připraven k použití.
2.  Pro každý blok dat v souboru:
    - Načtení šifrovaného bloku dat z trvalého úložiště.
    - Vygenerování klíče BEK z klíče DEK a šifrovaného bloku dat.
    - Dešifrování dat pomocí klíče BEK.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo algoritmus šifrování bloku dat:
1.  Zkontrolujte, jestli dek pro účet Data Lake Storage Gen1 je uložena do mezipaměti a připravenko k použití.
    - Pokud není, přečtení šifrovaného klíče DEK z trvalého úložiště a jeho odeslání do služby Key Vault k dešifrování. Uložení dešifrovaného klíče DEK v mezipaměti. Nyní je připraven k použití.
2.  Vygenerování jedinečného klíče BEK pro blok dat z klíče DEK.
3.  Šifrování datového bloku pomocí klíče BEK s použitím šifrování AES-256.
4.  Uložení šifrovaného datového bloku v trvalém úložišti.

> [!NOTE] 
> Klíč DEK je vždy uložený zašifrovaný klíčem MEK, ať už se jedná o trvalé médium nebo mezipaměť.

## <a name="key-rotation"></a>Obměna klíčů

Pokud používáte klíče spravované zákazníkem, můžete obměňovat klíč MEK. Informace o tom, jak nastavit účet Data Lake Storage Gen1 s klíči spravovanými zákazníky, najdete [v tématu Začínáme](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="prerequisites"></a>Požadavky

Při nastavujete data lake storage gen1 účet, jste se rozhodli použít vlastní klíče. Tuto možnost po vytvoření účtu nejde změnit. Následující postup předpokládá, že používáte klíče spravované zákazníkem (tedy že jste zvolili vlastní klíče ze služby Key Vault).

Všimněte si, že pokud použijete výchozí možnosti šifrování, vaše data jsou vždy šifrována pomocí klíčů spravovaných programem Data Lake Storage Gen1. V této možnosti nemáte možnost otáčet klíče, protože jsou spravovány datovým lakem Gen1.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>Jak otočit MEK v datovém úložišti jezera Gen1

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Přejděte na instanci Trezoru klíčů, ve které jsou uloženy vaše klíče přidružené k vašemu účtu Data Lake Storage Gen1. Vyberte **Klíče**.

    ![Snímek obrazovky služby Key Vault](./media/data-lake-store-encryption/keyvault.png)

3. Vyberte klíč přidružený k účtu Data Lake Storage Gen1 a vytvořte novou verzi tohoto klíče. Všimněte si, že Data Lake Storage Gen1 aktuálně podporuje pouze střídání klíčů na novou verzi klíče. Obměnu za jiný klíč nepodporuje.

   ![Snímek obrazovky okna Klíče se zvýrazněnou možností Nová verze](./media/data-lake-store-encryption/keynewversion.png)

4. Přejděte na účet Data Lake Storage Gen1 a vyberte **Šifrování**.

   ![Snímek obrazovky okna účtu Data Lake Storage Gen1 se zvýrazněným šifrováním](./media/data-lake-store-encryption/select-encryption.png)

5. Zobrazí se zpráva informující o dostupnosti nové verze klíče. Kliknutím na **Obměnit klíč** aktualizujte klíč na novou verzi.

   ![Snímek obrazovky okna Gen1 úložiště datového jezera se zvýrazněnou zprávou a klávesou Otočit](./media/data-lake-store-encryption/rotatekey.png)

Tato operace by neměla trvat déle než dvě minuty a nemělo by dojít k žádnému výpadku v důsledku obměny klíče. Po dokončení operace se začne používat nová verze klíče.

> [!IMPORTANT]
> Po dokončení operace obměny klíče se už k šifrování vašich dat nebude aktivně používat stará verze klíče.  Ve výjimečných případech neočekávaného selhání, které ovlivní dokonce i redundantní kopie vašich dat, se však můžou data obnovit ze zálohy, která stále používá starý klíč. Pokud chcete zajistit dostupnost dat i v těchto výjimečných případech, uchovávejte kopii předchozí verze šifrovacího klíče. Doporučené postupy pro plánování zotavení po havárii najdete v tématu Pokyny pro zotavení po [havárii pro data v gen1 úložiště datového jezera.](data-lake-store-disaster-recovery-guidance.md) 