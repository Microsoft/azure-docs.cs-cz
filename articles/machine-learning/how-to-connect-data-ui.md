---
title: Připojení k datům ve službě úložiště v Azure
titleSuffix: Azure Machine Learning
description: Vytvořte úložiště dat a datové sady pro zabezpečené připojení k datům ve službách úložiště v Azure pomocí Azure Machine Learning studia.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.custom: how-to
ms.openlocfilehash: 5ddfa2adbc9ec39949d7352903445407ff8e8881
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91542151"
---
# <a name="connect-to-data-with-the-azure-machine-learning-studio"></a>Připojení k datům pomocí Azure Machine Learning studia

V tomto článku se dozvíte, jak získat přístup k datům pomocí [Azure Machine Learning studia](overview-what-is-machine-learning-studio.md). Připojte se k datům ve službě úložiště v Azure pomocí [Azure Machine Learningch úložišť](how-to-access-data.md)dat a potom tato data zabalíte pro úkoly v pracovních postupech ML pomocí [Azure Machine Learning datových sad](how-to-create-register-datasets.md).

Následující tabulka definuje a shrnuje výhody datových úložišť a datových sad. 

|Objekt|Popis| Výhody|   
|---|---|---|
|Úložiště dat| Připojte se bezpečně k vaší službě úložiště v Azure, a to uložením informací o připojení, jako je ID předplatného a autorizace tokenu v [Key Vault](https://azure.microsoft.com/services/key-vault/) přidružené k pracovnímu prostoru. | Vzhledem k tomu, že jsou vaše informace bezpečně uložené, můžete <br><br> <li> &nbsp;Neumísťujte &nbsp; &nbsp; přihlašovací údaje pro ověřování &nbsp; nebo &nbsp; původní &nbsp; zdroje dat na riziko. <li> Už je nemusíte zakódovat ve svých skriptech.
|Datové sady| Vytvořením datové sady vytvoříte odkaz na umístění zdroje dat společně s kopií jeho metadat. U datových sad můžete, <br><br><li> Přístup k datům během školení modelu.<li> Sdílejte data a spolupracujte s ostatními uživateli.<li> Využijte open source knihovny, jako je PANDAS, pro zkoumání dat. | Vzhledem k tomu, že datové sady jsou vyhodnoceny laxně vytvářená a data zůstávají v jejím existujícím umístění, je <br><br><li>Uchovávejte v úložišti jednu kopii dat.<li> Neúčtují se žádné dodatečné náklady na úložiště. <li> Nehrozí neriziková neúmyslná změna původních zdrojů dat.<li>Zvýšení rychlosti výkonu pracovního postupu ML. 

Informace o tom, kde úložiště dat a datové sady vyhovují celkovému pracovnímu postupu pro přístup k datům v Azure Machine Learning, najdete v článku [zabezpečený přístup k datům](concept-data.md#data-workflow) .

Pro prostředí prvního kódu si přečtěte následující články, které používají [sadu Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true) :
* [Připojte se ke službám úložiště Azure s úložištěm dat](how-to-access-data.md). 
* [Vytvořte Azure Machine Learning datové sady](how-to-create-register-datasets.md). 

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

- Přístup k [Azure Machine Learning Studiu](https://ml.azure.com/).

- Pracovní prostor služby Azure Machine Learning. [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).

    -  Když vytvoříte pracovní prostor, kontejner objektů BLOB v Azure a sdílená složka Azure se automaticky zaregistrují jako úložiště dat do pracovního prostoru. Jsou pojmenované `workspaceblobstore` a `workspacefilestore` v uvedeném pořadí. Pokud je úložiště BLOB dostačující pro vaše potřeby, `workspaceblobstore` je nastavené jako výchozí úložiště dat a už je nakonfigurované pro použití. V opačném případě potřebujete účet úložiště v Azure s [podporovaným typem úložiště](how-to-access-data.md#matrix).
    

## <a name="create-datastores"></a>Vytváření úložiště dat

Z [těchto řešení Azure Storage](how-to-access-data.md#matrix)můžete vytvořit úložiště dat. **Pro Nepodporovaná řešení úložišť**a za účelem úspory nákladů na výstup dat během EXPERIMENTů ml je nutné [přesunout data](how-to-access-data.md#move) do podporovaného řešení úložiště Azure. [Přečtěte si další informace o úložišti dat](how-to-access-data.md). 



V několika krocích se Azure Machine Learning Studiu vytvoří nové úložiště dat.

> [!IMPORTANT]
> Pokud je váš účet úložiště dat ve virtuální síti, je potřeba, abyste zajistili, že Studio bude mít přístup k vašim datům. V tématu [izolace sítě & ochraně osobních údajů](how-to-enable-virtual-network.md#machine-learning-studio) se ujistěte, že jsou použité příslušné kroky konfigurace.

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com/).
1. V levém podokně v části **Spravovat**vyberte **úložiště** .
1. Vyberte **+ nové úložiště dat**.
1. Vyplňte formulář pro nové úložiště dat. Formulář se inteligentně aktualizuje na základě vašich výběrů pro typ úložiště Azure a typ ověřování. V [části přístup k úložišti a oprávněním](#access-validation) se dozvíte, kde najít přihlašovací údaje pro ověřování, které potřebujete k naplnění tohoto formuláře.

Následující příklad ukazuje, jak formulář vypadá při vytváření **úložiště dat objektů BLOB v Azure**:

![Formulář pro nové úložiště dat](media/how-to-connect-data-ui/new-datastore-form.png)

## <a name="create-datasets"></a>Vytvoření datových sad

Po vytvoření úložiště dat vytvořte datovou sadu pro interakci s daty. Datové sady zabalí vaše data do laxně vytvářená vyhodnoceného spotřebního objektu pro úlohy strojového učení, jako je například školení. [Přečtěte si další informace o datových sadách](how-to-create-register-datasets.md).

Existují dva typy datových sad, DataSet a TabularDataset. 
[Datové sady](how-to-create-register-datasets.md#filedataset) souborů vytvářejí odkazy na jeden nebo více souborů nebo na veřejné adresy URL. Zatímco [TabularDatasets](how-to-create-register-datasets.md#tabulardataset) reprezentuje vaše data v tabulkovém formátu. 

Následující kroky a animace ukazují, jak vytvořit datovou sadu v [Azure Machine Learning Studiu](https://ml.azure.com).

> [!Note]
> Datové sady vytvořené prostřednictvím Azure Machine Learning studia se automaticky zaregistrují do pracovního prostoru.

![Vytvoření datové sady pomocí uživatelského rozhraní](./media/how-to-connect-data-ui/create-dataset-ui.gif)

Vytvoření datové sady v studiu:
1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com/).
1. V části **assets (prostředky** ) v levém podokně vyberte datové **sady** .
1. Vyberte **vytvořit datovou sadu** a zvolte zdroj datové sady. V tomto zdroji můžou být místní soubory, úložiště dat, veřejné adresy URL nebo [otevřené datové sady Azure](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).
1. Vyberte **tabulkové** nebo **soubor** pro typ datové sady.
1. Výběrem **Další** otevřete formulář **úložiště dat a výběr souboru** . V tomto formuláři můžete vybrat, kde má být datová sada po vytvoření, a také vybrat datové soubory, které se mají použít pro datovou sadu.
    1. Pokud se data nachází ve virtuální síti, povolte přeskočit ověřování. Přečtěte si další informace o [izolaci virtuální sítě a ochraně osobních údajů](how-to-enable-virtual-network.md#machine-learning-studio).
1. Výběrem možnosti **Další** naplníte formuláře **nastavení a náhled** a **schéma** . jsou inteligentně vyplněné na základě typu souboru a můžete ještě před vytvořením těchto formulářů nakonfigurovat datovou sadu. 
1. Kliknutím na tlačítko **Další** zkontrolujte formulář **potvrdit podrobnosti** . Projděte si výběr a vytvořte pro datovou sadu volitelný datový profil. Přečtěte si další informace o [profilování dat](#profile).
1. Vytvoření datové sady dokončíte výběrem **vytvořit** .

<a name="profile"></a>

### <a name="data-profile-and-preview"></a>Profil dat a verze Preview

Po vytvoření datové sady ověřte pomocí následujících kroků profil a verzi Preview v studiu. 

1. Přihlášení k [Azure Machine Learning Studiu](https://ml.azure.com/)
1. V části **assets (prostředky** ) v levém podokně vyberte datové **sady** .
1. Vyberte název datové sady, kterou chcete zobrazit. 
1. Vyberte kartu **prozkoumat** . 
1. Vyberte kartu **Náhled** nebo **profil** . 

![Zobrazit profil datové sady a verzi Preview](./media/how-to-connect-data-ui/dataset-preview-profile.gif)

V rámci datové sady můžete získat velké množství různých souhrnných statistik, abyste ověřili, jestli je vaše datová sada připravená na ML. Pro nečíselné sloupce obsahují jenom základní statistiky, jako je min, Max a počet chyb. Pro číselné sloupce si můžete prohlédnout i jejich statistické momenty a odhadované quantiles. 

Konkrétně datový profil Azure Machine Learning datové sady zahrnuje:

>[!NOTE]
> Pro funkce s nepodstatnými typy se zobrazí prázdné položky.

|Statistický údaj|Popis
|------|------
|Příznak| Název sloupce, který je sumarizován.
|Profil| Vložená vizualizace na základě typu odvozeného. Například řetězce, logické hodnoty a data budou mít počty hodnot, zatímco desetinná místa (číslice) mají přibližné histogramy. To vám umožní získat rychlé porozumění distribuci dat.
|Distribuce typu| Počet vložené hodnoty typů v rámci sloupce. Hodnoty null jsou jejich vlastní typ, takže tato vizualizace je užitečná pro zjištění lichých nebo chybějících hodnot.
|Typ|Odvozený typ sloupce. Možné hodnoty jsou: řetězce, logické hodnoty, kalendářní data a desetinná místa.
|Minimum| Minimální hodnota sloupce Pro funkce, jejichž typ nemá základní řazení (například logické hodnoty), se zobrazí prázdné položky.
|Maximum| Maximální hodnota sloupce 
|Počet| Celkový počet chybějících a nechybějících položek ve sloupci
|Počet nechybějících| Počet položek ve sloupci, které nebyly nalezeny. Prázdné řetězce a chyby jsou považovány za hodnoty, takže nebudou přispívat k "nechybějícímu počtu".
|Kvantily| Přibližné hodnoty na jednotlivých Quantile, které poskytují smysl distribuce dat.
|Mean| Aritmetický průměr nebo průměr sloupce
|Směrodatná odchylka| Měření množství rozptýlení nebo variace dat tohoto sloupce.
|Variance| Měření, jak daleko rozprostření dat tohoto sloupce z průměrné hodnoty. 
|Případné| Měření, jak se liší data tohoto sloupce od normálního rozdělení.
|Špičat| Měření, jak často se data tohoto sloupce v porovnání s normální distribucí.

## <a name="storage-access-and-permissions"></a>Přístup k úložišti a oprávnění

Aby bylo zajištěno zabezpečené připojení ke službě Azure Storage, Azure Machine Learning vyžaduje, abyste měli oprávnění pro přístup k odpovídajícímu úložišti dat. Tento přístup závisí na přihlašovacích údajích používaných k registraci úložiště dat.

### <a name="virtual-network"></a>Virtuální síť

Pokud je váš účet úložiště dat ve **virtuální síti**, jsou potřeba další kroky konfigurace, abyste zajistili, že Azure Machine Learning má přístup k vašim datům. Pokud chcete zajistit, aby se při vytváření a registraci úložiště dat používaly příslušné kroky konfigurace, přečtěte si téma [izolace sítě & ochraně osobních údajů](how-to-enable-virtual-network.md#machine-learning-studio) .  

### <a name="access-validation"></a>Ověření přístupu

V **rámci počátečního procesu vytváření a registrace úložiště dat**Azure Machine Learning automaticky ověřuje, zda existuje základní služba úložiště a uživatel zadal objekt zabezpečení (uživatelské jméno, instanční objekt nebo token SAS), který má přístup k zadanému úložišti.

**Po vytvoření úložiště dat**se toto ověřování provede jenom u metod, které vyžadují přístup k podkladové kontejneru úložiště, **ne** při každém načtení objektů úložiště dat. K ověření dojde například v případě, že chcete stáhnout soubory z úložiště dat. Pokud ale chcete pouze změnit výchozí úložiště dat, pak k ověření nedojde.

K ověření přístupu k podkladové službě úložiště můžete zadat klíč účtu, tokeny sdíleného přístupu (SAS) nebo instanční objekt podle typu úložiště dat, které chcete vytvořit. V [matici typ úložiště](how-to-access-data.md#matrix) jsou uvedené podporované typy ověřování, které odpovídají jednotlivým typům úložiště dat.

Klíč účtu, token SAS a informace o instančním objektu najdete na svém [Azure Portal](https://portal.azure.com).

* Pokud plánujete použít klíč účtu nebo token SAS pro ověřování, v levém podokně vyberte **účty úložiště** a zvolte účet úložiště, který chcete zaregistrovat.
  * Stránka **Přehled** poskytuje informace, jako je název účtu, kontejner a název sdílené složky.
      1. Klíče účtu najdete v části **přístupové klíče** v podokně **Nastavení** .
      1. V případě tokenů SAS přejděte na **sdílené přístupové podpisy** v podokně **Nastavení** .

* Pokud máte v úmyslu použít [instanční objekt](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) k ověřování, otevřete **Registrace aplikací** a vyberte aplikaci, kterou chcete použít.
    * Jeho odpovídající stránka **přehledu** bude obsahovat požadované informace, jako je ID TENANTA a ID klienta.

> [!IMPORTANT]
> Z bezpečnostních důvodů možná budete muset změnit přístupové klíče pro účet Azure Storage (klíč účtu nebo token SAS). V takovém případě nezapomeňte nové přihlašovací údaje synchronizovat s vaším pracovním prostorem a úložištěm dat, která jsou k němu připojená. Přečtěte si, jak [synchronizovat aktualizované přihlašovací údaje](how-to-change-storage-access-key.md).

### <a name="permissions"></a>Oprávnění

V případě kontejneru objektů blob Azure a Azure Data Lake úložiště Gen 2 se ujistěte, že přihlašovací údaje mají přístup ke **čtečce dat objektů BLOB úložiště** . Přečtěte si další informace o [čtečce dat objektů BLOB úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader). 

## <a name="train-with-datasets"></a>Trénování s datovými sadami

Využijte své datové sady ve zkušebních experimentech Machine Learning pro školení modelů ML. [Další informace o tom, jak naučit s datovými sadami](how-to-train-with-datasets.md)

## <a name="next-steps"></a>Další kroky

* [Podrobný příklad školení pomocí TabularDatasets a automatizovaného strojového učení](tutorial-first-experiment-automated-ml.md).

* [Výuka modelu](how-to-set-up-training-targets.md).

* Další příklady školení pro datovou sadu najdete v [ukázkových poznámkových blocích](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
