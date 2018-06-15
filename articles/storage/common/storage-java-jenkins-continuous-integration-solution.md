---
title: Používání Azure Storage s řešením volaných průběžnou integraci | Microsoft Docs
description: V tomto kurzu ukazují, jak používat službu objektů blob v Azure jako úložiště pro sestavení artefakty vytvořené volaných průběžnou integraci řešení.
services: storage
documentationcenter: java
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: f4e5ca75-f6cb-4f74-981b-2aa06bb8de45
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 02/28/2017
ms.author: seguler
ms.openlocfilehash: 174ac449e803ed5327468a38ea7264cb9923a877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874133"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Použití Azure Storage s řešením Jenkins Continuous Integration
## <a name="overview"></a>Přehled
Následující informace ukazuje, jak používat úložiště objektů Blob jako úložiště artefaktů sestavení vytvořené řešení volaných nepřetržité integrace (CI) nebo jako zdroj soubory ke stažení pro použití v procesu sestavení. Mezi scénáře, kde jste by to užitečné je, když jste kódování agilní vývojovém prostředí (pomocí Java nebo jiné jazyky), sestavení běží v závislosti na průběžnou integraci a budete potřebovat úložiště pro sestavení artefaktů, aby můžete, například je sdílet se členy jiné organizace, vašim zákazníkům nebo udržujte archiv. Další možností je, pokud samotné úlohu sestavení vyžaduje další soubory, například závislosti stáhnout jako součást sestavení vstup.

V tomto kurzu budete používat modul plug-in úložiště Azure pro nepřetržitou Integraci volaných zpřístupněné společností Microsoft.

## <a name="overview-of-jenkins"></a>Přehled volaných
Volaných průběžnou integraci softwaru projektu umožňuje vývojářům snadno integrovat jejich změny kódu a je tvořen sestavení automaticky a často, a tím zvýšit produktivitu vývojáři. Sestavení jsou verzí a sestavení artefaktů je možné uložit do různých úložišť. Toto téma se ukazují, jak používat úložiště objektů blob v Azure jako úložiště artefaktů sestavení. Zobrazí také stažení závislosti z Azure blob storage.

Další informace o volaných lze najít na [splňovat volaných](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Výhody použití služby objektů Blob
Výhody použití služby objektů Blob k hostování sestavení artefaktů agilní vývoj patří:

* Vysoká dostupnost artefaktů sestavení nebo ke stažení závislostí.
* Výkon, pokud je vaše řešení volaných CI ukládání artefaktů sestavení.
* Výkon, pokud zákazníci a partneři stažení artefaktů sestavení.
* Ovládat zásady přístupu uživatelů s volbou mezi anonymní přístup, přístup na základě vypršení platnosti sdílený přístupový podpis, privátní přístup, atd.

## <a name="prerequisites"></a>Požadavky
Budete potřebovat následující používat službu objektů Blob s vaším řešením volaných CI:

* Průběžnou integraci volaných řešení.
  
    Pokud aktuálně nemáte volaných CI řešení, můžete spustit volaných CI řešení pomocí takto:
  
  1. Na počítači povolené Java, stáhněte si jenkins.war z <http://jenkins-ci.org>.
  2. Na příkazovém řádku, který se otevírá do složky, která obsahuje jenkins.war spusťte příkaz:
     
      `java -jar jenkins.war`

  3. V prohlížeči otevřete `http://localhost:8080/`. Tím se otevře řídicí panel volaných, který použijete k instalaci a konfiguraci modulu plug-in Azure Storage.
     
      Při typických volaných CI řešení by nastavit tak, aby spustit jako služby, spuštěné volaných war na příkazovém řádku jsou dostatečné pro účely tohoto kurzu.
* Účet Azure. Můžete zaregistrovat k účtu Azure v <http://www.azure.com>.
* Účet úložiště Azure. Pokud nemáte účet úložiště, můžete vytvořit jeden pomocí kroků v [vytvořit účet úložiště](../common/storage-create-storage-account.md#create-a-storage-account).
* Znalost volaných CI řešení je doporučená, ale není vyžadována, jako v následujícím obsahu použije základní příklad ukázat vám, že kroky potřebné při použití služby objektů Blob jako úložiště pro nepřetržitou Integraci volaných sestavení artefaktů.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Použití služby objektů Blob s volaných CI
Pomocí služby objektů Blob s volaných, budete muset nainstalovat modul plug-in Azure Storage, nakonfigurovat modul plug-in použít váš účet úložiště a pak vytvořte akce po sestavení, která odesílá do účtu úložiště artefaktů sestavení. Tyto kroky jsou popsané v následujících částech.

## <a name="how-to-install-the-azure-storage-plugin"></a>Postup instalace modulu plug-in Azure Storage
1. V rámci volaných řídicí panel, klikněte na **spravovat volaných**.
2. V **spravovat volaných** klikněte na tlačítko **Správa modulů plug-in**.
3. Klikněte **dostupné** kartě.
4. V **artefaktů Uploaders** část, zkontrolujte **modul plug-in služby Microsoft Azure Storage**.
5. Klikněte na možnost **nainstalovat bez restartování** nebo **stáhnout a nainstalovat po restartování**.
6. Restartujte volaných.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Postup konfigurace modulu plug-in Azure Storage k použití vašeho účtu úložiště
1. V rámci volaných řídicí panel, klikněte na **spravovat volaných**.
2. V **spravovat volaných** klikněte na tlačítko **nakonfigurujte systém**.
3. V **konfigurace účtu úložiště Microsoft Azure** části:
   1. Zadejte název účtu úložiště, který můžete získat z [portálu Azure](https://portal.azure.com).
   2. Zadejte klíč účtu úložiště, také dosažitelný z [portálu Azure](https://portal.azure.com).
   3. Použijte výchozí hodnotu pro **adresu URL koncového bodu služby objektů Blob** Pokud používáte veřejného cloudu Azure. Pokud používáte jiný Azure cloud, použijte koncový bod jako zadaný v [portálu Azure](https://portal.azure.com) pro váš účet úložiště. 
   4. Klikněte na tlačítko **ověření přihlašovacích údajů úložiště** k ověření svého účtu úložiště. 
   5. [Nepovinné] Pokud máte další účty úložiště, které chcete k dispozici pro vaše volaných CI, klikněte na tlačítko **přidat další účty úložiště**.
   6. Klikněte na tlačítko **Uložit** uložte nastavení.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Jak vytvořit akce po sestavení, která odesílá artefaktů sestavení do vašeho účtu úložiště
Pro účely instrukce nejprve budeme potřebovat pro vytvoření úlohy, který bude vytvořit několik souborů a poté přidejte v akci po sestavení k nahrání souborů do účtu úložiště.

1. Na řídicím panelu Jenkinse klikněte na **New Item** (Nová položka).
2. Název úlohy **MyJob**, klikněte na tlačítko **sestavení projektu, uvolněte stylu softwaru**a potom klikněte na **OK**.
3. V **sestavení** části úlohy konfigurace, klikněte na tlačítko **přidat krok sestavení** a zvolte **dávkové spuštění Windows příkaz**.
4. V **příkaz**, použijte následující příkazy:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. V **akce po sestavení** části úlohy konfigurace, klikněte na tlačítko **přidat akci po sestavení** a zvolte **artefakty nahrát do Azure Blob storage**.
6. Pro **název účtu úložiště**, vyberte účet úložiště, který chcete použít.
7. Pro **název kontejneru**, zadejte název kontejneru. (Kontejneru budou vytvořeny, pokud již neexistuje když jsou odeslány artefaktů sestavení.) Můžete použít proměnné prostředí, tak v tomto příkladu zadejte **${JOB_NAME}** jako název kontejneru.
   
    **Tip**
   
    Níže **příkaz** část, kde jste zadali skript pro **dávkové spuštění Windows příkaz** je odkaz na objekt environment variables rozpoznáno volaných. Klikněte na tento odkaz na další názvy proměnných prostředí a popisy. Všimněte si, že proměnné prostředí, které obsahují zvláštní znaky, jako například **BUILD_URL** proměnné prostředí, nejsou povoleny jako název kontejneru nebo běžné virtuální cestu.
8. Klikněte na tlačítko **zveřejnit nový kontejner ve výchozím nastavení** v tomto příkladu. (Pokud budete chtít použít privátní kontejner, musíte vytvořit sdílený přístupový podpis pro povolení přístupu. To je nad rámec tohoto tématu. Další informace o sdílených přístupových podpisů v [pomocí sdíleného přístupového podpisy (SAS)](../storage-dotnet-shared-access-signature-part-1.md).)
9. [Nepovinné] Klikněte na tlačítko **čisté kontejneru před nahráním** Pokud chcete, aby kontejneru vymazán obsah, než se odešlou artefaktů sestavení (nechte nezaškrtnuté Pokud nechcete vyčistit obsahu kontejneru).
10. Pro **seznamu artefakty nahrát**, zadejte  **text /*.txt**.
11. Pro **běžné virtuální cestu pro nahraném artefakty**, pro účely tohoto kurzu, zadejte **${sestavení\_ID} / ${sestavení\_číslo}**.
12. Klikněte na tlačítko **Uložit** uložte nastavení.
13. Na řídicím panelu volaných klikněte na tlačítko **sestavení teď** ke spuštění **MyJob**. Zkontrolujte výstup konzoly stavu. Stavové zprávy pro úložiště Azure bude výstup konzoly součástí, při spuštění akce po sestavení nahrát artefaktů sestavení.
14. Po úspěšném dokončení úlohy můžete zkontrolovat otevřením veřejného objektu blob artefaktů sestavení.
    1. Přihlášení k [portál Azure](https://portal.azure.com).
    2. Klikněte na tlačítko **úložiště**.
    3. Klikněte na název účtu úložiště, který jste použili pro volaných.
    4. Klikněte na tlačítko **kontejnery**.
    5. Klikněte na kontejner s názvem **myjob**, což je malá verze název úlohy, který jste přiřadili při vytvoření úlohy volaných. Názvy kontejnerů a objektů blob jsou malá písmena (a malá a velká písmena) v úložišti Azure. V seznamu objektů blob pro kontejner s názvem **myjob** byste měli vidět **hello.txt** a **date.txt**. Zkopírujte adresu URL pro kteroukoli z těchto položek a otevřete jej v prohlížeči. Zobrazí se textový soubor, který byl odeslán jako artefaktů sestavení.

Jednu úlohu lze vytvořit pouze jeden akce po sestavení, která odesílá artefakty do Azure blob storage. Všimněte si, že jedné akce po sestavení artefaktů nahrát do Azure blob storage můžete zadat různé soubory (včetně zástupné znaky) a cesty k souborům v rámci **seznamu artefakty nahrát** pomocí středníkem jako oddělovač. Například pokud vaše volaných sestavení vytváří JAR soubory a soubory TXT v pracovním prostoru **sestavení** a vy chcete nahrát i do Azure blob storage, použijte následující **seznamu artefakty nahrát** hodnota: **sestavení nebo\*.jar; sestavení nebo\*.txt**. Syntaxe dvěma dvojtečkami také můžete zadat cestu pro použití v rámci název objektu blob. Například, pokud chcete, aby JAR získat nahrát pomocí **binární soubory** v cestě objektu blob a soubory TXT získat nahrát pomocí **oznámení** v cestě objektu blob, použít pro následující **seznamu artefakty nahrát** hodnota: **sestavení nebo\*. jar::binaries; sestavení nebo\*. txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Postup vytvoření krok sestavení, která stáhne z Azure blob storage
Následující kroky ukazují, jak nakonfigurovat krok sestavení ke stažení položek z Azure blob storage. To může být užitečné, pokud chcete zahrnout položky v buildu, například JAR, která uchovává v Azure blob storage.

1. V **sestavení** části úlohy konfigurace, klikněte na tlačítko **přidat krok sestavení** a zvolte **stažení ze služby Azure Blob storage**.
2. Pro **název účtu úložiště**, vyberte účet úložiště, který chcete použít.
3. Pro **název kontejneru**, zadejte název kontejneru, který obsahuje objekty BLOB, které chcete stáhnout. Můžete použít proměnné prostředí.
4. Pro **název objektu Blob**, zadejte název objektu blob. Můžete použít proměnné prostředí. Navíc můžete použít hvězdičku, jako zástupný znak po zadání počátečního písmena názvu objektu blob. Například **projektu\***  by zadejte všech objektů BLOB, jejichž názvy začínají **projektu**.
5. [Nepovinné] Pro **cestu pro stažení**, zadejte cestu v volaných počítače, ve které chcete stáhnout soubory z Azure blob storage. Proměnné prostředí lze také použít. (Pokud nezadáte hodnotu **cestu pro stažení**, soubory z Azure blob storage se stáhnou do pracovního prostoru úlohy.)

Pokud máte další položky, které chcete stáhnout z Azure blob storage, můžete vytvořit další kroky.

Po spuštění sestavení, můžete zkontrolovat výstup konzoly historie sestavení nebo podívejte se na umístění pro stahování, pokud chcete zobrazit, zda byly úspěšně staženy objekty BLOB, které jste očekávali.  

## <a name="components-used-by-the-blob-service"></a>Komponenty používané stránkami služby objektů Blob
Následující část obsahuje přehled součásti služby objektů Blob.

* **Účet služby Storage:** Veškerý přístup ke službě Azure Storage se provádí prostřednictvím účtu úložiště. Toto je nejvyšší úroveň oboru názvů pro přístup k objektům BLOB. Účet může obsahovat neomezený počet kontejnerů, tak dlouho, dokud jejich celková velikost je v části 100TB.
* **Kontejner**: kontejner zajišťuje seskupení sady objektů BLOB. Všechny objekty blob musí být v kontejneru. Účet může obsahovat neomezený počet kontejnerů. Kontejner můžete pojmout neomezený počet objektů blob.
* **Objekt BLOB**: soubor libovolného typu a velikosti. Existují dva typy objektů BLOB, které mohou být uloženy ve službě Azure Storage: objekty BLOB bloků a stránek. Většina souborů jsou objekty BLOB bloku. Objekt blob jeden blok může být až do velikosti 200GB. Tento kurz používá objekty BLOB bloku. Objekty BLOB stránky, jiný typ objektu blob, může být až 1TB velikosti a jsou efektivnější, když jsou často upravit rozsah bajtů v souboru. Další informace o objekty BLOB najdete v tématu [Principy objekty BLOB bloku a doplňovacích objektů BLOB, objekty BLOB stránky](http://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Formát adresy URL**: objekty BLOB jsou adresovatelné v následujícím formátu adresy URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Ve formátu výše uvedené platí pro veřejného cloudu Azure. Pokud používáte jiný Azure cloud, použijte koncového bodu v rámci [portálu Azure](https://portal.azure.com) k určení váš koncový bod adresy URL.)
  
    Ve výše uvedené, formátu `storageaccount` představuje název účtu úložiště `container_name` představuje název kontejneru, a `blob_name` představuje název objektu blob služby v uvedeném pořadí. V rámci název kontejneru, může mít více cest, oddělených lomítkem,  **/** . Název kontejneru příklad v tomto kurzu se **MyJob**, a **${sestavení\_ID} / ${sestavení\_číslo}** byl použit pro běžné virtuální cestu, což vede k objektu blob s adresu URL v následujícím formátu:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="next-steps"></a>Další kroky
* [Splňovat volaných](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
* [Úložiště Azure SDK pro jazyk Java](https://github.com/azure/azure-storage-java)
* [Odkaz na sadu SDK klienta úložiště Azure](http://dl.windowsazure.com/storage/javadoc/)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog týmu Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)

Další informace najdete na webu [Azure pro vývojáře v Javě](/java/azure).