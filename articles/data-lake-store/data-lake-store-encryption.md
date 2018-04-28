---
title: Šifrování v Azure Data Lake Store | Dokumentace Microsoftu
description: Šifrování v Azure Data Lake Store pomáhá chránit vaše data, implementovat zásady podnikového zabezpečení a splnit požadavky na dodržování legislativních předpisů. Tento článek poskytuje přehled návrhu a popisuje některé technické aspekty implementace.
services: data-lake-store
documentationcenter: ''
author: esung22
manager: ''
editor: ''
ms.assetid: ''
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: 2328f7e233025d9f9ee9113aa28fb74754dd9193
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="encryption-of-data-in-azure-data-lake-store"></a>Šifrování dat v Azure Data Lake Store

Šifrování v Azure Data Lake Store pomáhá chránit vaše data, implementovat zásady podnikového zabezpečení a splnit požadavky na dodržování legislativních předpisů. Tento článek poskytuje přehled návrhu a popisuje některé technické aspekty implementace.

Data Lake Store podporuje šifrování jak neaktivních uložených dat, tak i přenášených dat. Pro neaktivní uložená data podporuje služba Data Lake Store „ve výchozím nastavení zapnuté“ transparentní šifrování. Tady je podrobnější vysvětlení významu těchto termínů:

* **Ve výchozím nastavení zapnuté:** Při vytváření nového účtu Data Lake Store výchozí nastavení povolí šifrování. Následně se data ukládaná ve službě Data Lake Store budou šifrovat vždy před uložením na trvalé médium. Toto chování platí pro veškerá data a po vytvoření účtu nejde změnit.
* **Transparentní:** Data Lake Store data automaticky šifruje před uložením a dešifruje před načtením. Konfiguraci a správu šifrování provádí správce na úrovni služby Data Lake Store. Rozhraní API pro přístup k datům se nemění. Proto není nutné kvůli šifrování měnit aplikace a služby, které pracují se službou Data Lake Store.

Přenášená data se ve službě Data Lake Store také vždy šifrují. Kromě šifrování dat před uložením na trvalé médium se také vždy šifrují přenášená data pomocí protokolu HTTPS. HTTPS je jediný protokol, který se podporuje v rozhraních REST služby Data Lake Store. Následující diagram ukazuje, jak probíhá šifrování dat ve službě Data Lake Store:

![Diagram šifrování dat ve službě Data Lake Store](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-store"></a>Nastavení šifrování ve službě Data Lake Store

Šifrování se pro službu Data Lake Store nastavuje během vytváření účtu a ve výchozím nastavení je vždy povoleno. Klíče můžete buď spravovat sami, nebo můžete službě Data Lake Store umožnit, aby je spravovala za vás (to je výchozí nastavení).

Další informace najdete v tématu [Začínáme](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-store"></a>Jak funguje šifrování ve službě Data Lake Store

Následující informace se týkají správy hlavních šifrovacích klíčů a vysvětlují tři různé typy klíčů, které můžete použít k šifrování dat ve službě Data Lake Store.

### <a name="master-encryption-keys"></a>Hlavní šifrovací klíče

Data Lake Store nabízí dva režimy pro správu hlavních šifrovacích klíčů (MEK). Prozatím předpokládejme, že hlavní šifrovací klíč je klíč nejvyšší úrovně. K dešifrování jakýchkoli dat uložených ve službě Data Lake Store se vyžaduje přístup k hlavnímu šifrovacímu klíči.

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
|Kdo je vlastníkem instance služby Key Vault a hlavního šifrovacího klíče?|Služba Data Lake Store|Vlastníte instanci služby Key Vault, která patří do vašeho předplatného Azure. Hlavní šifrovací klíč ve službě Key Vault může být spravovaný softwarem nebo hardwarem.|
|Je možné službě Data Lake Store odebrat přístup k hlavnímu šifrovacímu klíči?|Ne|Ano. Můžete spravovat seznamy řízení přístupu ve službě Key Vault a pro službu Data Lake Store odebrat záznamy řízení přístupu k identitě služby.|
|Je možné trvale odstranit hlavní šifrovací klíč?|Ne|Ano. Pokud odstraníte hlavní šifrovací klíč ze služby Key Vault, data v účtu Data Lake Store nebude možné dešifrovat, a to ani službou Data Lake Store. <br><br> Pokud jste hlavní šifrovací klíč před odstraněním ze služby Key Vault explicitně zazálohovali, je možné ho obnovit a následně obnovit i data. Pokud jste ale hlavní šifrovací klíč před odstraněním ze služby Key Vault nezazálohovali, data v účtu Data Lake Store již nikdy nebude možné dešifrovat.|


Kromě tohoto rozdílu, tedy kdo spravuje hlavní šifrovací klíče a instanci služby Key Vault, ve které se nachází, je zbytek návrhu pro oba režimy stejný.

Při výběru režimu pro hlavní šifrovací klíče je důležité pamatovat na následující:

*   Možnost výběru, jestli chcete použít klíče spravované zákazníkem nebo klíče spravované službou, máte při zřizování účtu Data Lake Store.
*   Po zřízení účtu Data Lake Store už režim nejde změnit.

### <a name="encryption-and-decryption-of-data"></a>Šifrování a dešifrování dat

V návrhu šifrování dat používají tři typy klíčů. Následující tabulka poskytuje souhrn:

| Klíč                   | Zkratka | Přidružený k | Umístění úložiště                             | Typ       | Poznámky                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Hlavní šifrovací klíč | MEK          | Účet Data Lake Store | Key Vault                              | Asymetrický | Může být spravovaný službou Data Lake Store nebo vámi.                                                              |
| Šifrovací klíč dat   | DEK          | Účet Data Lake Store | Trvalé úložiště spravované službou Data Lake Store | Symetrický  | Klíč DEK je šifrovaný klíčem MEK. Na trvalé médium se ukládá šifrovaný klíč DEK. |
| Šifrovací klíč bloku  | BEK          | Blokem dat | Žádný                                         | Symetrický  | Klíč BEK je odvozený od klíče DEK a příslušného datového bloku.                                                      |

Následující diagram znázorňuje tyto koncepty:

![Klíče v šifrování dat](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo algoritmus dešifrování souboru:
1.  Kontrola, jestli je klíč DEK pro účet Data Lake Store uložený v mezipaměti a připravený k použití.
    - Pokud není, přečtení šifrovaného klíče DEK z trvalého úložiště a jeho odeslání do služby Key Vault k dešifrování. Uložení dešifrovaného klíče DEK v mezipaměti. Nyní je připraven k použití.
2.  Pro každý blok dat v souboru:
    - Načtení šifrovaného bloku dat z trvalého úložiště.
    - Vygenerování klíče BEK z klíče DEK a šifrovaného bloku dat.
    - Dešifrování dat pomocí klíče BEK.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo algoritmus šifrování bloku dat:
1.  Kontrola, jestli je klíč DEK pro účet Data Lake Store uložený v mezipaměti a připravený k použití.
    - Pokud není, přečtení šifrovaného klíče DEK z trvalého úložiště a jeho odeslání do služby Key Vault k dešifrování. Uložení dešifrovaného klíče DEK v mezipaměti. Nyní je připraven k použití.
2.  Vygenerování jedinečného klíče BEK pro blok dat z klíče DEK.
3.  Šifrování datového bloku pomocí klíče BEK s použitím šifrování AES-256.
4.  Uložení šifrovaného datového bloku v trvalém úložišti.

> [!NOTE] 
> Klíč DEK je vždy uložený zašifrovaný klíčem MEK, ať už se jedná o trvalé médium nebo mezipaměť.

## <a name="key-rotation"></a>Obměna klíčů

Pokud používáte klíče spravované zákazníkem, můžete obměňovat klíč MEK. Informace o nastavení účtu Data Lake Store s použitím klíčů spravovaných zákazníkem najdete v tématu [Začínáme](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="prerequisites"></a>Požadavky

Při nastavování účtu Data Lake Store jste zvolili použití vlastních klíčů. Tuto možnost po vytvoření účtu nejde změnit. Následující postup předpokládá, že používáte klíče spravované zákazníkem (tedy že jste zvolili vlastní klíče ze služby Key Vault).

Pamatujte, že pokud použijete výchozí možnosti šifrování, vaše data se vždy šifrují pomocí klíčů spravovaných službou Data Lake Store. Při této možnosti nemůžete obměňovat klíče, protože je spravuje služba Data Lake Store.

### <a name="how-to-rotate-the-mek-in-data-lake-store"></a>Obměna klíče MEK ve službě Data Lake Store

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přejděte do instance služby Key Vault, ve které jsou uloženy klíče přidružené k vašemu účtu Data Lake Store. Vyberte **Klíče**.

    ![Snímek obrazovky služby Key Vault](./media/data-lake-store-encryption/keyvault.png)

3.  Vyberte klíč přidružený k vašemu účtu Data Lake Store a vytvořte novou verzi tohoto klíče. Pamatujte, že Data Lake Store aktuálně podporuje pouze obměnu klíče za novou verzi klíče. Obměnu za jiný klíč nepodporuje.

   ![Snímek obrazovky okna Klíče se zvýrazněnou možností Nová verze](./media/data-lake-store-encryption/keynewversion.png)

4.  Přejděte do účtu úložiště Data Lake Store a vyberte **Šifrování**.

    ![Snímek obrazovky okna účtu úložiště Data Lake Store se zvýrazněnou možností Šifrování](./media/data-lake-store-encryption/select-encryption.png)

5.  Zobrazí se zpráva informující o dostupnosti nové verze klíče. Kliknutím na **Obměnit klíč** aktualizujte klíč na novou verzi.

    ![Snímek obrazovky okna Data Lake Store se zvýrazněnou zprávou a možností Obměnit klíč](./media/data-lake-store-encryption/rotatekey.png)

Tato operace by neměla trvat déle než dvě minuty a nemělo by dojít k žádnému výpadku v důsledku obměny klíče. Po dokončení operace se začne používat nová verze klíče.

> [!IMPORTANT]
> Po dokončení operace obměny klíče se už k šifrování vašich dat nebude aktivně používat stará verze klíče.  Ve výjimečných případech neočekávaného selhání, které ovlivní dokonce i redundantní kopie vašich dat, se však můžou data obnovit ze zálohy, která stále používá starý klíč. Pokud chcete zajistit dostupnost dat i v těchto výjimečných případech, uchovávejte kopii předchozí verze šifrovacího klíče. Osvědčené postupy pro plánování zotavení po havárii najdete v tématu [Pokyny pro zotavení po havárii pro data ve službě Data Lake Store](data-lake-store-disaster-recovery-guidance.md). 