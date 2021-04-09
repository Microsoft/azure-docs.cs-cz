---
title: Jak kontrolovat intervaly Amazon S3
description: V této příručce najdete podrobné informace o tom, jak kontrolovat intervaly Amazon S3.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/21/2021
ms.custom: references_regions
ms.openlocfilehash: f77bd69f8266d9461481cd0a12a7b70107622de5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773449"
---
# <a name="azure-purview-connector-for-amazon-s3"></a>Konektor Azure dosah pro Amazon S3

Tento průvodce popisuje, jak pomocí Azure dosah kontrolovat nestrukturovaná data, která jsou aktuálně uložená v sadách Amazon S3 Standard, a zjišťují, jaké typy citlivých informací ve vašich datech existují. Tato příručka také popisuje, jak identifikovat bloky Amazon S3, kde jsou data v současnosti uložená, aby byla snadno chráněná ochrana informací a kompatibilita dat.

Pro tuto službu použijte dosah a poskytněte účet Microsoft se zabezpečeným přístupem k AWS, kde se skener dosah spustí. Dosah skener používá tento přístup k vašim kontejnerům Amazon S3 ke čtení vašich dat a sestavy výsledků kontroly, včetně pouze metadat a klasifikace, zpět do Azure. Pomocí sestav klasifikace a označování dosah můžete analyzovat a kontrolovat výsledky kontroly dat.

V této příručce se dozvíte, jak přidat sady Amazon S3 jako prostředky dosah a vytvořit vyhledávání pro data Amazon S3.

## <a name="purview-scope-for-amazon-s3"></a>Obor dosah pro Amazon S3

Následující obor je specifický pro registraci a skenování sad Amazon S3 jako dosah zdrojů dat.

|Obor  |Popis  |
|---------|---------|
|**Omezení dat**     |    Služba dosah Scanner v současné době podporuje kontrolu intervalů Amazon S3 až 100 GB dat na každého tenanta.     |
|**Typy souborů**     | Služba dosah Scanner v současné době podporuje následující typy souborů: <br><br>. Avro,. csv,. doc,. DOCM,. docx,. dot,. JSON,. odp,. ODS,. odt,. orc,. Parquet,. PDF,. pot,. PPS,. ppsx,........,. pptx,. PSV,. SSV,. TSV,. txt,. xlc,. xls,. xlsb,. xlsm,. xlsx,. xlt,. XML        |
|**Oblasti**     | Konektor dosah pro službu Amazon S3 je aktuálně nasazen pouze v oblastech **AWS USA – východ (Ohio)** a **Evropa (Frankfurt)** . <br><br>Další informace najdete v tématu [oblast úložiště a skenování](#storage-and-scanning-regions).   |
|     |         |

Další informace najdete v zdokumentovaných dosah limitech v těchto tématech:

- [Správa a zvýšení kvót pro prostředky pomocí Azure dosah](how-to-manage-quotas.md)
- [Podporované zdroje dat a typy souborů v Azure dosah](sources-and-scans.md)
### <a name="storage-and-scanning-regions"></a>Oblasti úložiště a skenování

V následující tabulce jsou namapovány oblasti, ve kterých se data ukládají do oblasti, kde by byla prověřena službou Azure dosah.

> [!IMPORTANT]
> Zákazníkům se budou účtovat všechny související poplatky za přenos dat podle oblasti jejich kontejneru.
>

| Oblast úložiště | Oblast vyhledávání |
| ------------------------------- | ------------------------------------- |
| USA – východ (Ohio)                  | USA – východ (Ohio)                        |
| USA – východ (N. ) – Virginia           | USA – východ (Ohio) nebo USA – východ (N. ) – Virginia                       |
| USA – západ (N. Kalifornii         | USA – východ (Ohio)                        |
| USA – západ (Oregon)                | USA – východ (Ohio)                        |
| Afrika (kapverdské město)              | Evropa (Frankfurt)                    |
| Asie a Tichomoří (Hongkong)        | Evropa (Frankfurt) nebo Asie a Tichomoří (Sydney)                   |
| Asie a Tichomoří (Bombaj)           | Evropa (Frankfurt) nebo Asie a Tichomoří (Sydney)                   |
| Asie a Tichomoří (Ósaka-Local)      | Evropa (Frankfurt) nebo Asie a Tichomoří (Sydney)                   |
| Asie a Tichomoří (Soul)            | Evropa (Frankfurt) nebo Asie a Tichomoří (Sydney)                   |
| Asie a Tichomoří (Singapur)        | Evropa (Frankfurt) nebo Asie a Tichomoří (Sydney)                   |
| Asie a Tichomoří (Sydney)           | Evropa (Frankfurt) nebo Asie a Tichomoří (Sydney)                  |
| Asie a Tichomoří (Tokio)            | Evropa (Frankfurt) nebo Asie a Tichomoří (Sydney)                 |
| Kanada (střed)                | USA – východ (Ohio)                        |
| Čína (Peking)                 | Nepodporováno                    |
| Čína (NingXia)                 | Nepodporováno                   |
| Evropa (Frankfurt)              | Evropa (Frankfurt)                    |
| Evropa (Irsko)                | Evropa (Frankfurt) nebo Evropa (Irsko)                   |
| Evropa (Londýn)                 | Evropa (Frankfurt) nebo Evropa (Irsko)                   |
| Evropa (Milán)                  | Evropa (Frankfurt)                    |
| Evropa (Paříž)                  | Evropa (Frankfurt)                    |
| Evropa (Stockholm)              | Evropa (Frankfurt)                    |
| Střední východ (Bahrajn)           | Evropa (Frankfurt)                    |
| Jižní Amerika (Svatý Paulo)       | USA – východ (Ohio)                        |
| | |

## <a name="prerequisites"></a>Požadavky

Před přidáním vašich bloků Amazon S3 jako zdrojů dat dosah a kontrolou dat S3 ověřte, že jste provedli následující požadavky.

- Musíte být správcem zdroje dat služby Azure dosah.

- Při přidávání vašich kontejnerů jako prostředků dosah budete potřebovat hodnoty [AWS ARN](#retrieve-your-new-role-arn), [název kontejneru](#retrieve-your-amazon-s3-bucket-name)a někdy vaše [ID účtu AWS](#locate-your-aws-account-id).

### <a name="create-a-purview-account"></a>Vytvoření účtu dosah

- **Pokud již máte účet dosah,** můžete pokračovat v konfiguracích vyžadovaných pro podporu AWS S3. Začněte [vytvořením přihlašovacích údajů dosah pro kontrolu intervalu AWS](#create-a-purview-credential-for-your-aws-bucket-scan).

- **Pokud potřebujete vytvořit účet dosah,** postupujte podle pokynů v tématu [vytvoření instance účtu Azure dosah](create-catalog-portal.md). Po vytvoření účtu se sem vraťte k dokončení konfigurace a začněte používat konektor dosah pro Amazon S3.

### <a name="create-a-purview-credential-for-your-aws-bucket-scan"></a>Vytvoření dosah přihlašovacích údajů pro kontrolu intervalu AWS

Tento postup popisuje, jak vytvořit nové dosah přihlašovací údaje, které se použijí při kontrole AWS sad.

> [!TIP]
> Při [konfiguraci kontroly](#create-a-scan-for-your-amazon-s3-bucket)můžete také vytvořit nové přihlašovací údaje v průběhu procesu. V takovém případě v poli **pověření** vyberte možnost **Nový**.
>

1. V dosah přejděte do **centra pro správu** a v části **zabezpečení a přístup** vyberte **přihlašovací údaje**.

1. Vyberte **Nový** a v podokně **nové přihlašovací údaje** , které se zobrazí na pravé straně, k vytvoření přihlašovacích údajů dosah použijte následující pole:

    |Pole |Popis  |
    |---------|---------|
    |**Název**     |Zadejte smysluplný název pro toto pověření nebo použijte výchozí nastavení.        |
    |**Popis**     |Zadejte volitelný popis tohoto přihlašovacího údaje, například `Used to scan the tutorial S3 buckets`         |
    |**Metoda ověřování**     |Vyberte **role ARN**, protože pro přístup k vašemu kontejneru používáte ARN role.         |
    |**ID účet Microsoft**     |Kliknutím zkopírujete tuto hodnotu do schránky. Při [vytváření ARN role v AWS](#create-a-new-aws-role-for-purview)použijte tuto hodnotu jako **ID účet Microsoft** .           |
    |**Externí ID**     |Kliknutím zkopírujete tuto hodnotu do schránky. Při [vytváření ARN role v AWS](#create-a-new-aws-role-for-purview) použijte tuto hodnotu jako **externí ID** .        |
    |**Role ARN**     | Po [vytvoření role Amazon IAM](#create-a-new-aws-role-for-purview)přejděte do vaší role v oblasti IAM, zkopírujte hodnotu **role ARN** a zadejte ji sem. Příklad: `arn:aws:iam::284759281674:role/S3Role`. <br><br>Další informace najdete v tématu [načtení nové role ARN](#retrieve-your-new-role-arn). |
    | | |

    Až skončíte s vytvářením přihlašovacích údajů, vyberte **vytvořit** .

Další informace o přihlašovacích údajích dosah najdete v [dokumentaci ke službě Azure dosah Public Preview](manage-credentials.md).

### <a name="create-a-new-aws-role-for-purview"></a>Vytvoření nové role AWS pro dosah

1.  Otevřete konzolu **Amazon Web Services** a v části **zabezpečení, identita a kompatibilita** vyberte **IAM**.

1. Vyberte **role** a pak **vytvořit roli**.

1. Vyberte **jiný účet AWS** a zadejte následující hodnoty:

    |Pole  |Description  |
    |---------|---------|
    |**Account ID**     |    Zadejte ID účtu Microsoft. Příklad: `615019938638`     |
    |**Externí ID**     |   V části Možnosti vyberte **vyžadovat externí ID...** a potom do určeného pole zadejte své externí ID. <br>Příklad: `e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`    <br><br>Externí ID můžete najít při.  |
    | | |

    > [!NOTE]
    > Hodnoty pro **ID účtu Microsoft** i **externí ID** můžete najít v oblasti pověření **centra pro správu** dosah  >   , kde jste [vytvořili přihlašovací údaje pro dosah](#create-a-purview-credential-for-your-aws-bucket-scan).
    >

    Například:

    ![Přidejte ID účtu Microsoft k účtu AWS.](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. V oblasti **vytvořit roli > připojit zásady oprávnění** filtrujte oprávnění zobrazená na **S3**. Vyberte **AmazonS3ReadOnlyAccess** a potom vyberte **Další: značky**.

    ![Vyberte zásady ReadOnlyAccess pro novou skenovací roli Amazon S3.](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

1. V oblasti **Přidat značky (volitelné)** můžete volitelně zvolit vytvoření smysluplné značky pro tuto novou roli. Užitečné značky umožňují organizovat, sledovat a řídit přístup pro každou roli, kterou vytvoříte.

    V případě potřeby zadejte nový klíč a hodnotu pro značku. Až budete hotovi, nebo pokud chcete tento krok přeskočit, vyberte **Další:** Projděte si podrobnosti role a dokončete vytvoření role.

    ![Přidejte smysluplnou značku k uspořádání, sledování nebo řízení přístupu pro novou roli.](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. V oblasti **recenze** udělejte toto:

    - Do pole **název role** zadejte smysluplný název pro vaši roli.
    - V poli **Popis role** zadejte volitelný popis, který identifikuje účel role.
    - V části **zásady** potvrďte, že je k této roli připojená správná zásada (**AmazonS3ReadOnlyAccess**).

    Potom vyberte **vytvořit roli** a proces dokončete.

    Například:

    ![Před vytvořením role si Projděte podrobnosti.](./media/register-scan-amazon-s3/review-role.png)


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>Konfigurace vyhledávání šifrovaných sad Amazon S3

AWS intervaly podporují více typů šifrování. U kontejnerů, které používají šifrování **AWS-KMS** , je pro povolení kontroly nutná speciální konfigurace.

> [!NOTE]
> V případě kontejnerů, které nepoužívají šifrování S3, šifrování AES-256 nebo AWS-KMS, přeskočte tuto část a pokračujte [v načítání názvu kontejneru Amazon S3](#retrieve-your-amazon-s3-bucket-name).
>

**Pokud chcete kontrolovat typ šifrování, který se používá v kontejnerech Amazon S3:**

1. V AWS přejděte do **úložiště**  >  **S3** > a v nabídce vlevo vyberte **intervaly** .

    ![Vyberte kartu sady Amazon S3.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Vyberte kontejner, který chcete kontrolovat. Na stránce s podrobnostmi o daném intervalu vyberte kartu **vlastnosti** a přejděte dolů k oblasti **výchozí šifrování** .

    - Pokud je vámi vybraný interval nakonfigurovaný tak, aby **AWS** šifrování bez přípony klíčů, včetně toho, jestli je výchozí šifrování pro váš kontejner **zakázané**, přeskočte zbývající část tohoto postupu a pokračujte s [získáním názvu sady Amazon S3](#retrieve-your-amazon-s3-bucket-name).

    - Pokud je vámi vybraný interval nakonfigurovaný pro šifrování **AWS-KMS** , pokračujte tak, jak je popsáno níže, a přidejte novou zásadu, která umožňuje kontrolu sady pomocí vlastního šifrování **AWS-služby správy klíčů** .

    Například:

    ![Zobrazení kontejneru Amazon S3 nakonfigurovaného šifrování AWS-KMS](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**Pokud chcete přidat novou zásadu, která povolí kontrolu intervalu pomocí vlastního šifrování AWS:**

1. V AWS přejděte na **služby**  >     >   **zásady** IAM a vyberte **vytvořit zásadu**.

1. Na kartě **vytvořit**  >  **vizuální Editor** zásad Definujte zásadu s následujícími hodnotami:

    |Pole  |Description  |
    |---------|---------|
    |**Služba**     |  Zadejte a vyberte službu **správy klíčů**.       |
    |**Akce**     | V části **úroveň přístupu** vyberte možnost **zapsat** . tím se rozbalí oddíl **Write** .<br>Po rozbalení vyberte jenom možnost **dešifrování** .        |
    |**Prostředky**     |Vyberte konkrétní prostředek nebo **všechny prostředky**.         |
    | | |

    Až budete hotovi, vyberte **zkontrolovat zásadu** a pokračujte.

    ![Vytvořte zásadu pro skenování kontejneru pomocí šifrování AWS-KMS.](./media/register-scan-amazon-s3/create-policy-kms.png)

1. Na stránce **Kontrola zásad** zadejte smysluplný název zásady a volitelný popis a pak vyberte **vytvořit zásadu**.

    Nově vytvořená zásada se přidá do seznamu zásad.

1. Připojte nové zásady k roli, kterou jste přidali ke skenování.

    1. Přejděte zpět na stránku   >  **role** IAM a vyberte roli, kterou jste přidali [dříve](#create-a-new-aws-role-for-purview).

    1. Na kartě **oprávnění** vyberte **připojit zásady**.

        ![Na kartě oprávnění role vyberte připojit zásady.](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. Na stránce **připojit oprávnění** vyhledejte a vyberte novou zásadu, kterou jste vytvořili výše. Vyberte **připojit zásadu** a připojte zásadu k roli.

        Stránka **Souhrn** je aktualizována s novou zásadou připojenou k vaší roli.

        ![Umožňuje zobrazit aktualizovanou stránku souhrnu s novou zásadou připojenou k vaší roli.](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>Načtení nové role ARN

Když [vytváříte kontrolu pro váš kontejner Amazon S3](#create-a-scan-for-your-amazon-s3-bucket), budete muset zaznamenat svou roli AWS ARN a zkopírovat ji do dosah.

**Postup načtení ARN role:**

1. V oblasti role AWS **Správa identit a přístupu (IAM)**  >   vyhledejte a vyberte novou roli, kterou jste [vytvořili pro dosah](#create-a-purview-credential-for-your-aws-bucket-scan).

1. Na stránce **Souhrn** role vyberte tlačítko **Kopírovat do schránky** napravo od hodnoty **ARN role** .

    ![Zkopírujte hodnotu role ARN do schránky.](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

1. Vložte tuto hodnotu do zabezpečeného umístění, které je připravené k použití při [vytváření kontroly pro váš kontejner Amazon S3](#create-a-scan-for-your-amazon-s3-bucket).

### <a name="retrieve-your-amazon-s3-bucket-name"></a>Načíst název intervalu Amazon S3

Když [vytváříte kontrolu pro váš kontejner Amazon S3](#create-a-scan-for-your-amazon-s3-bucket) , budete potřebovat název svého kontejneru Amazon S3.

**Chcete-li načíst název svého kontejneru:**

1. V AWS přejděte do **úložiště**  >  **S3** > a v nabídce vlevo vyberte **intervaly** .

    ![Zobrazte kartu sady Amazon S3.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Vyhledejte a vyberte svou sadu, abyste zobrazili stránku s podrobnostmi o intervalu, a pak zkopírujte název kontejneru do schránky.

    Například:

    ![Načtěte a zkopírujte adresu URL bloku S3.](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    Vložte název svého kontejneru do zabezpečeného souboru a přidejte do `s3://` něj předponu, abyste mohli vytvořit hodnotu, kterou budete muset zadat při konfiguraci vašeho intervalu jako prostředku dosah.

    Příklad: `s3://purview-tutorial-bucket`

> [!NOTE]
> Jako zdroj dat dosah je podporována pouze kořenová úroveň vašeho kontejneru. Například následující adresa URL, která obsahuje podsložku, *není podporována:*`s3://purview-tutorial-bucket/view-data`
>

### <a name="locate-your-aws-account-id"></a>Vyhledejte ID účtu AWS.

Budete potřebovat ID účtu AWS k registraci účtu AWS jako zdroje dat dosah spolu se všemi jeho kontejnery.

ID účtu AWS je ID, pomocí kterého se přihlašujete ke konzole AWS. Můžete ji také najít, až se přihlásíte na řídicím panelu IAM, vlevo pod možností navigace a nahoře, jako číselnou část přihlašovací adresy URL:

Například:

![Načtěte ID účtu AWS.](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>Přidání jednoho intervalu Amazon S3 jako prostředku dosah

Tento postup použijte, pokud máte pouze jeden interval S3, který chcete zaregistrovat do dosah jako zdroj dat, nebo pokud máte ve svém účtu AWS více kontejnerů, ale nechcete je zaregistrovat do dosah.

1. Spusťte portál dosah pomocí vyhrazeného konektoru dosah pro adresu URL pro Amazon S3. Tuto adresu URL vám poskytl tým správy produktů dosah konektoru Amazon S3.

    ![Spusťte portál dosah.](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Přejděte na stránku **zdroje** Azure dosah a vyberte ikona **zaregistrovat** ![ registraci.](./media/register-scan-amazon-s3/register-button.png) > **Amazon S3**  >  **Pokračovat**.

    ![Přidejte sadu Amazon AWS jako zdroj dat dosah.](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > Pokud máte více [kolekcí](manage-data-sources.md#manage-collections) a chcete přidat organizaci Amazon S3 do určité kolekce, vyberte **zobrazení mapa** v pravém horním rohu a pak vyberte ikonu **zaregistrovat** ![ registraci.](./media/register-scan-amazon-s3/register-button.png) tlačítko uvnitř kolekce
    >

1. V podokně **Registrovat zdroje (Amazon S3)** , které se otevře, zadejte následující podrobnosti:

    |Pole  |Popis  |
    |---------|---------|
    |**Název**     |Zadejte smysluplný název, nebo použijte zadaný výchozí.         |
    |**Adresa URL kontejneru**     | Pomocí následující syntaxe zadejte adresu URL AWS sady:   `s3://<bucketName>`     <br><br>**Poznámka**: Ujistěte se, že používáte jenom kořenovou úroveň vašeho kontejneru bez jakýchkoli podsložek. Další informace najdete v tématu [načtení názvu kontejneru Amazon S3](#retrieve-your-amazon-s3-bucket-name). |
    |**Vybrat kolekci** |Pokud jste vybrali možnost Registrovat zdroj dat v rámci kolekce, již je tato kolekce uvedena. <br><br>Vyberte jinou kolekci podle potřeby, **žádné** pro přiřazení žádné kolekce nebo **novou** vytvořte novou kolekci nyní. <br><br>Další informace o dosah kolekcích najdete v tématu [Správa zdrojů dat v Azure dosah](manage-data-sources.md#manage-collections).|
    | | |

    Až budete hotovi, vyberte **Dokončit** a dokončete registraci.

Pokračujte tím, [že vytvoříte kontrolu pro váš kontejner Amazon S3.](#create-a-scan-for-your-amazon-s3-bucket)

## <a name="add-all-of-your-amazon-s3-buckets-as-purview-resources"></a>Přidání všech vašich sad Amazon S3 jako prostředků dosah

Tento postup použijte v případě, že máte ve svém účtu Amazon více bloků S3 a chcete zaregistrovat všechny jako dosah zdroje dat.

1. Spusťte portál dosah pomocí vyhrazeného konektoru dosah pro adresu URL pro Amazon S3. Tuto adresu URL vám poskytl tým správy produktů dosah konektoru Amazon S3.

    ![Spustit konektor pro vyhrazený dosah portál Amazon S3](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Přejděte na stránku **zdroje** Azure dosah a vyberte ikona **zaregistrovat** ![ registraci.](./media/register-scan-amazon-s3/register-button.png) > **Účty Amazon**  >  **Pokračovat**.

    ![Přidejte účet Amazon jako zdroj dat dosah.](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > Pokud máte více [kolekcí](manage-data-sources.md#manage-collections) a chcete přidat organizaci Amazon S3 do určité kolekce, vyberte **zobrazení mapa** v pravém horním rohu a pak vyberte ikonu **zaregistrovat** ![ registraci.](./media/register-scan-amazon-s3/register-button.png) tlačítko uvnitř kolekce
    >

1. V podokně **Registrovat zdroje (Amazon S3)** , které se otevře, zadejte následující podrobnosti:

    |Pole  |Popis  |
    |---------|---------|
    |**Název**     |Zadejte smysluplný název, nebo použijte zadaný výchozí.         |
    |**ID účtu AWS**     | Zadejte ID účtu AWS. Další informace najdete v tématu [Vyhledání ID účtu AWS](#locate-your-aws-account-id) .|
    |**Vybrat kolekci** |Pokud jste vybrali možnost Registrovat zdroj dat v rámci kolekce, již je tato kolekce uvedena. <br><br>Vyberte jinou kolekci podle potřeby, **žádné** pro přiřazení žádné kolekce nebo **novou** vytvořte novou kolekci nyní. <br><br>Další informace o dosah kolekcích najdete v tématu [Správa zdrojů dat v Azure dosah](manage-data-sources.md#manage-collections).|
    | | |

    Až budete hotovi, vyberte **Dokončit** a dokončete registraci.

Pokračujte tím, [že vytvoříte kontrolu pro váš kontejner Amazon S3](#create-a-scan-for-your-amazon-s3-bucket).

## <a name="create-a-scan-for-your-amazon-s3-bucket"></a>Vytvoření kontroly pro váš kontejner Amazon S3

Po přidání sad jako zdrojů dat dosah můžete nakonfigurovat kontrolu tak, aby se spouštěla v naplánovaných intervalech, nebo hned.

1. Přejděte do oblasti **zdroje** dosah Azure a proveďte jednu z následujících akcí:

    - V **zobrazení mapy** vyberte možnost **Nová kontrola** ![ Ikona nové kontroly.](./media/register-scan-amazon-s3/new-scan-button.png) do pole zdroj dat.
    - V **zobrazení seznamu** umístěte ukazatel myši na řádek zdroje dat a vyberte **Nový skenovat** ![ Ikona nové kontroly. ](./media/register-scan-amazon-s3/new-scan-button.png)

1. V podokně **Prohledat...** , které se otevře vpravo, definujte následující pole a pak vyberte **pokračovat**:

    |Pole  |Popis  |
    |---------|---------|
    |**Název**     |  Zadejte smysluplný název pro kontrolu nebo použijte výchozí nastavení.       |
    |**Typ** |Zobrazí se pouze v případě, že jste přidali svůj účet AWS se všemi zahrnutými intervaly. <br><br>Aktuální možnosti zahrnují jenom **všechny**  >  **Amazon S3**. Můžete zůstat vyladěné pro další možnosti, které můžete vybrat, když se dosah paleta podpory pro rozšíření. |
    |**Přihlašovací údaj**     |  Vyberte dosah přihlašovací údaje s vaší rolí ARN. <br><br>**Tip**: Pokud chcete v tuto chvíli vytvořit nové přihlašovací údaje, vyberte **Nový**. Další informace najdete v tématu [Vytvoření dosah přihlašovacích údajů pro kontrolu intervalu AWS](#create-a-purview-credential-for-your-aws-bucket-scan).     |
    |     |         |

    Dosah automaticky zkontroluje, jestli je role ARN platná a že je kontejner a objekt v rámci tohoto kontejneru dostupný, a pak pokračuje v případě, že je připojení úspěšné.

    > [!TIP]
    > Chcete-li před pokračováním zadat jiné hodnoty a otestovat připojení sami, vyberte možnost **Test připojení** v pravém dolním rohu před výběrem možnosti **pokračovat**.
    >

1. V podokně **vybrat sadu pravidel skenování** buď vyberte sadu výchozích pravidel **AmazonS3** , nebo vyberte **Nová sada pravidel skenování** a vytvořte novou sadu vlastních pravidel. Po výběru sady pravidel vyberte **pokračovat**.

    Pokud se rozhodnete vytvořit novou sadu pravidel pro vlastní skenování, pomocí Průvodce definujte následující nastavení:

    |Podokno  |Description  |
    |---------|---------|
    |**Sada pravidel nové kontroly** /<br>**Popis pravidla skenování**    |   Zadejte smysluplný název a volitelný popis pro sadu pravidel.      |
    |**Vybrat typy souborů**     | Vyberte všechny typy souborů, které chcete zahrnout do kontroly, a pak vyberte **pokračovat**.<br><br>Chcete-li přidat nový typ souboru, vyberte možnost **nový typ souboru** a definujte následující: <br>– Přípona souboru, kterou chcete přidat <br>– Volitelný popis  <br>– Zda má obsah souboru vlastní oddělovač, nebo se jedná o typ systémového souboru. Pak zadejte vlastní oddělovač nebo vyberte typ systémového souboru. <br><br>Vyberte **vytvořit** a vytvořte vlastní typ souboru.     |
    |**Vybrat pravidla klasifikace**     |   Přejděte na a vyberte pravidla klasifikace, která chcete pro datovou sadu spustit.      |
    |     |         |

    Vyberte **vytvořit** , až budete hotovi s vytvořením sady pravidel.

1. V podokně **nastavit aktivační událost kontroly** vyberte jednu z následujících možností a potom vyberte **pokračovat**:

    - **Opakovaná** konfigurace plánu pro opakovanou kontrolu
    - **Jakmile** nakonfigurujete kontrolu, která se spustí hned

1. V podokně **zkontrolovat prohledání** zkontrolujte podrobnosti kontroly a potvrďte, že jsou správné, a pak vyberte **Uložit** nebo **Uložit a spustit** , pokud jste v předchozím **podokně vybrali jednu** z nich.

    > [!NOTE]
    > Po spuštění může trvat až 24 hodin, než se kontrola dokončí. Budete moct zkontrolovat **sestavy Insight** a vyhledat v katalogu 24 hodin po spuštění každé kontroly.
    >

Další informace najdete v tématu [prozkoumání výsledků kontroly dosah](#explore-purview-scanning-results).

## <a name="explore-purview-scanning-results"></a>Prozkoumat výsledky prohledávání dosah

Po dokončení kontroly dosah v kontejnerech Amazon S3 můžete přejít k podrobnostem v oblasti **zdroje** dosah a zobrazit historii prohledávání.

Vyberte zdroj dat, pro který chcete zobrazit jeho podrobnosti, a pak vyberte kartu **kontroly** , abyste zobrazili všechny aktuálně spuštěné nebo dokončené kontroly.
Pokud jste přidali účet AWS s více intervaly, zobrazí se pod účtem historie kontroly pro jednotlivé intervaly.

Například:

![Zobrazení kontrol intervalu AWS S3 v rámci zdroje účtu AWS](./media/register-scan-amazon-s3/account-scan-history.png)

Pomocí dalších oblastí dosah můžete zjistit podrobnosti o obsahu v nemovitosti, včetně vašich sad Amazon S3:

- **Vyhledejte v katalogu data dosah** a vyfiltrujte konkrétní interval. Například:

    ![Vyhledejte v katalogu prostředky AWS S3.](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- **Zobrazte si sestavy Insight** , abyste zobrazili statistiku pro klasifikaci, popisky citlivosti, typy souborů a další podrobnosti o vašem obsahu.

    Všechny sestavy dosah Insight zahrnují výsledky kontroly Amazon S3, včetně zbývajících výsledků z vašich zdrojů dat Azure. V případě potřeby byl do možností filtrování sestav přidán další typ assetu **Amazon S3** .

    Další informace najdete v tématu [Principy přehledů v Azure dosah](concept-insights.md).

## <a name="next-steps"></a>Další kroky

Další informace o sestavách Azure dosah Insight:

> [!div class="nextstepaction"]
> [Principy přehledů v Azure Purview](concept-insights.md)
