---
title: Jak používat Hudson s úložištěm objektů Blob | Dokumentace Microsoftu
description: Popisuje, jak použít Hudson s úložištěm objektů Blob v Azure jako úložiště artefaktů sestavení.
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 02/28/2017
ms.author: seguler
ms.component: common
ms.openlocfilehash: c076ae96f8aba648196dc5222db3da3da68673ff
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528387"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Použití Azure Storage s řešením Hudson Continuous Integration
## <a name="overview"></a>Přehled
Tyto informace ukazuje, jak používat úložiště objektů Blob jako úložiště artefaktů sestavení vytvořených řešením Hudson Continuous Integration (CI), nebo jako zdrojové soubory ke stažení pro použití v procesu sestavení. Mezi scénáře, ve kterém by pro vás to užitečné při jste psaní kódu v prostředí agilního vývoje (pomocí sad Java SDK nebo jiných jazyků), jsou sestavení spuštěna založené na průběžné integrace a potřebujete úložiště artefaktů sestavení, tak, aby vám může, například je sdílet s ostatními členy organizace vašim zákazníkům nebo udržujte archiv.  Další možností je úlohy sestavení samotné vyžaduje další soubory, například závislosti stáhnout jako část sestavení vstup.

V tomto kurzu budete používat modul plug-in Azure Storage pro Hudson CI k dispozici společností Microsoft.

## <a name="introduction-to-hudson"></a>Úvod do Hudson
Hudson umožňuje průběžnou integraci softwarovém projektu umožňuje vývojářům snadno integrovat svoje změny kódu a je tvořen sestavení automaticky a často, a tím zvýšit produktivitu vývojářů. Sestavení se systémovou správou verzí a artefakty sestavení může být odeslán do různých úložišť. Tento článek vám ukáže, jak používat úložiště objektů Blob v Azure jako úložiště artefaktů sestavení. Zobrazí také ke stažení závislosti z úložiště objektů Blob v Azure.

Další informace o Hudson najdete [splňovat Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Výhody použití služby Blob service
Výhody použití služby Blob service pro hostování artefakty sestavení agilního vývoje patří:

* Vysokou dostupnost vašich artefaktů sestavení a/nebo ke stažení závislostí.
* Výkon, pokud je vaše řešení Hudson CI odesílá artefakty sestavení.
* Výkon, pokud je vaše zákazníky a partnery stažení artefaktů sestavení.
* Kontrolu nad zásad přístupu uživatele, s možností volby mezi anonymní přístup, na základě vypršení platnosti sdílený přístupový podpis přístup, privátní přístup, atd.

## <a name="prerequisites"></a>Požadavky
Budete potřebovat následující příkaz pro použití služby Blob service s řešením Hudson CI:

* Řešení Hudson Continuous Integration.
  
    Pokud momentálně nemáte k dispozici řešení Hudson CI, můžete spustit řešení Hudson CI následujícím způsobem:
  
  1. Na počítači s podporou Javy, stáhněte si WAR Hudson z <http://hudson-ci.org/>.
  2. Na příkazovém řádku, který se otevírá ve složce, která obsahuje Hudson WAR spusťte Hudson WAR. Například, pokud jste si stáhli verzi 3.1.2:
     
      `java -jar hudson-3.1.2.war`

  3. V prohlížeči otevřete `http://localhost:8080/`. Otevře se řídicí panel Hudson.
  4. Při prvním použití Hudson, dokončete počáteční nastavení na `http://localhost:8080/`.
  5. Po dokončete počáteční nastavení, zrušit spuštěné instance Hudson WAR, znovu spusťte Hudson WAR a znovu otevřete tak řídicí panel Hudson `http://localhost:8080/`, který použijete k instalaci a konfiguraci modulu plug-in Azure Storage.
     
      Během typického řešení Hudson CI by nastavit tak, aby se spouštěl jako služba s Hudson war na příkazovém řádku jsou dostatečné pro účely tohoto kurzu.
* Účet Azure. Můžete se zaregistrovat pro účet Azure na <http://www.azure.com>.
* Účet úložiště Azure. Pokud ještě nemáte účet úložiště, můžete vytvořit pomocí postupu v [vytvořit účet úložiště](../common/storage-create-storage-account.md#create-a-storage-account).
* Seznámení se s Hudson CI řešení se doporučuje, ale není nutné, protože následující obsah bude používat jako základní příklad zobrazit kroky potřebné při použití služby Blob service pro Hudson CI jako úložiště artefaktů sestavení.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Použití služby Blob service s Hudson CI
Použití služby Blob service s Hudson, budete muset nainstalovat modul plug-in Azure Storage, konfigurace modulu plug-in použít svůj účet úložiště a pak vytvořte akce po sestavení, která odesílá artefakty sestavení do účtu úložiště. Tyto kroky jsou popsány v následujících částech.

## <a name="how-to-install-the-azure-storage-plugin"></a>Jak nainstalovat modul plug-in Azure Storage
1. V rámci Hudson řídicí panel, klikněte na tlačítko **spravovat Hudson**.
2. Na **spravovat Hudson** klikněte na **Správa modulů plug-in**.
3. Klikněte na tlačítko **dostupné** kartu.
4. Klikněte na tlačítko **ostatní**.
5. V **artefaktů Nahrávajících** vyberte **modul plug-in Microsoft Azure Storage**.
6. Klikněte na **Nainstalovat**.
7. Po dokončení instalace restartujte Hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Jak nakonfigurovat modul plug-in Azure Storage, aby používalo účet úložiště
1. V rámci Hudson řídicí panel, klikněte na tlačítko **spravovat Hudson**.
2. Na **spravovat Hudson** klikněte na **konfigurovat systém**.
3. V **konfigurací účtu úložiště Microsoft Azure** části:
   
    a. Zadejte název svého účtu úložiště, který můžete získat z [webu Azure Portal](https://portal.azure.com).
   
    b. Zadejte klíč účtu úložiště, také dají získat od [webu Azure Portal](https://portal.azure.com).
   
    c. Použijte výchozí hodnotu pro **adresu URL koncového bodu služby objektů Blob** Pokud používáte ve veřejném cloudu Azure. Pokud používáte jiný cloud Azure, použijte koncový bod uvedená v [webu Azure Portal](https://portal.azure.com) pro váš účet úložiště.
   
    d. Klikněte na tlačítko **ověřit přihlašovací údaje úložiště** k ověření svého účtu úložiště.
   
    e. [Volitelné] Pokud máte další účty úložiště, které chcete zpřístupnit Hudson CI, klikněte na tlačítko **přidat další účty úložiště**.
   
    f. Klikněte na tlačítko **Uložit** uložte nastavení.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Jak vytvořit akce po sestavení, která odesílá artefakty sestavení do účtu úložiště
Pro účely instrukce nejprve potřebujeme vytvořit úlohu, která vytvoří několik souborů a potom přidat do akce po sestavení k nahrání souborů do účtu úložiště.

1. V rámci Hudson řídicí panel, klikněte na tlačítko **nová úloha**.
2. Název úlohy **MyJob**, klikněte na tlačítko **sestavení softwaru volný styl projektu**a potom klikněte na tlačítko **OK**.
3. V **sestavení** část konfigurace úlohy můžete kliknout na tlačítko **přidat krok sestavení** a zvolte **spuštění Windows dávkového příkazu**.
4. V **příkaz**, použijte následující příkazy:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. V **akcích po sestavení** část konfigurace úlohy můžete kliknout na tlačítko **odešlete artefakty do služby Microsoft Azure Blob storage**.
6. Pro **název účtu úložiště**, vyberte účet úložiště.
7. Pro **název kontejneru**, zadejte název kontejneru. (Kontejneru se vytvoří, pokud již neexistuje při odeslání artefaktů sestavení.) Můžete použít proměnné prostředí, takže v tomto příkladu zadejte **${hodnotu JOB_NAME}** jako název kontejneru.
   
    **Tip**
   
    Níže **příkaz** části, kde jste zadali skript pro **spuštění Windows dávkového příkazu** je odkaz na objekt environment variables rozpoznávaných Hudson. Kliknutím na tento odkaz na další popisy a názvy proměnných prostředí. Všimněte si, že proměnné prostředí, které obsahují speciální znaky, jako například **BUILD_URL** proměnné prostředí, nejsou povoleny jako běžné virtuální cestu a název kontejneru.
8. Klikněte na tlačítko **zveřejnit nový kontejner ve výchozím nastavení** pro účely tohoto příkladu. (Pokud chcete použít kontejner privátní, bude potřeba vytvořit sdílený přístupový podpis pro povolení přístupu. To je nad rámec tohoto článku. Další informace o sdílených přístupových podpisů v [použití sdílených přístupových podpisů (SAS)](../storage-dotnet-shared-access-signature-part-1.md).)
9. [Volitelné] Klikněte na tlačítko **čisté kontejneru před nahráním** Pokud chcete, aby kontejneru vymazání obsahu předtím, než se nahrají artefakty sestavení (políčko nechte nezaškrtnuté Pokud nechcete vyčistit obsah kontejneru).
10. Pro **seznamu artefaktů k odeslání**, zadejte **text/*.txt**.
11. Pro **běžné virtuální cestu pro nahrané artefakty**, zadejte **${sestavení\_ID} a$ {sestavení\_číslo}**.
12. Klikněte na tlačítko **Uložit** uložte nastavení.
13. Na řídicím panelu Hudson, klikněte na tlačítko **Build Now** spuštění **MyJob**. Prohlédněte si výstup konzoly stav. Stavové zprávy pro službu Azure Storage se zahrnou výstup konzoly při spuštění akce po sestavení k odeslání artefaktů sestavení.
14. Po úspěšném dokončení úlohy můžete zkontrolovat artefakty sestavení tak, že otevřete veřejných objektů blob.
    
    a. Přihlaste se k [Portálu Azure](https://portal.azure.com).
    
    b. Klikněte na tlačítko **úložiště**.
    
    c. Klikněte na název účtu úložiště, který jste použili pro Hudson.
    
    d. Klikněte na tlačítko **kontejnery**.
    
    e. Klikněte na kontejner s názvem **myjob**, což je malá verzi název úlohy, který jste přiřadili při vytváření úlohy Hudson. Názvy kontejnerů a objektů blob názvy jsou malá písmena (a malá a velká písmena) ve službě Azure Storage. V seznamu objektů blob pro kontejner s názvem **myjob** byste měli vidět **hello.txt** a **date.txt**. Zkopírujte adresu URL pro některý z těchto položek a otevřít ji v prohlížeči. Zobrazí se textový soubor, který byl nahrán jako artefakt sestavení.

Za úlohu lze vytvořit pouze jednu akci po sestavení, který odešle artefakty do služby Azure Blob storage. Všimněte si, že jedné akce po sestavení k nahrání artefakty do úložiště objektů Blob v Azure můžete zadat různé soubory (včetně zástupných znaků) a cesty k souborům v rámci **seznamu artefaktů k odeslání** oddělte středníky jako oddělovač. Například pokud je vaše Hudson sestavení vytvoří soubory JAR a soubory TXT v pracovním prostoru **sestavení** složce a chcete nahrát i do Azure Blob storage, použijte následující **seznamu artefaktů k odeslání** hodnota: **sestavení /\*.jar; sestavení /\*.txt**. Syntaxe dvěma dvojtečkami také můžete zadat cestu pro použití v rámci názvu objektu blob. Například, pokud chcete, aby kromě souborů JAR získat odeslán pomocí **binární soubory** cesta objektu blob a soubory TXT se nahrát pomocí **oznámení** v cestě objektů blob použít následující **seznamu artefaktů Nahrát** hodnota: **sestavení /\*. jar::binaries; sestavení /\*. txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Jak vytvořit krok sestavení, které stáhne ze služby Azure Blob storage
Následující kroky ukazují, jak nakonfigurovat krok sestavení ke stažení položek z úložiště objektů Blob v Azure. To může být užitečné, pokud chcete zahrnout položky ve vašem sestavení, například kromě souborů JAR, které udržují v úložišti objektů Blob v Azure.

1. V **sestavení** část konfigurace úlohy můžete kliknout na tlačítko **přidat krok sestavení** a zvolte **stáhnout z úložiště objektů Blob v Azure**.
2. Pro **název účtu úložiště**, vyberte účet úložiště.
3. Pro **název kontejneru**, zadejte název kontejneru, který obsahuje objekty BLOB, kterou chcete stáhnout. Můžete použít proměnné prostředí.
4. Pro **název objektu Blob**, zadejte název objektu blob. Můžete použít proměnné prostředí. Navíc můžete použít hvězdičku, jako zástupný znak po zadání počátečního písmena názvu objektu blob. Například **projektu\***  zadáte všechny objekty BLOB, jejichž jména začínají **projektu**.
5. [Volitelné] Pro **cestu pro stažení**, zadejte cestu na počítači Hudson, kam chcete soubory stáhnout z úložiště objektů Blob v Azure. Můžete také použít proměnné prostředí. (Pokud nezadáte hodnotu **cestu pro stažení**, stáhnou se soubory z úložiště objektů Blob v Azure do pracovního prostoru úlohy.)

Pokud máte další položky, které chcete stáhnout z úložiště objektů Blob v Azure, můžete vytvořit další kroky sestavení.

Po spuštění sestavení, můžete zkontrolovat výstup na konzole historie sestavení nebo podívejte se na vaše umístění stahování, pokud chcete zobrazit, zda byly úspěšně staženy objekty BLOB, které jste očekávali.

## <a name="components-used-by-the-blob-service"></a>Součásti, které používají službu Blob service
Následující body nabízí přehled komponent služby objektů Blob.

* **Účet úložiště**: veškerý přístup ke službě Azure Storage se provádí prostřednictvím účtu úložiště. Toto je nejvyšší úroveň oboru názvů pro přístup k objektům BLOB. Účet může obsahovat neomezený počet kontejnerů, jejich celkové velikosti je v části 100 TB.
* **Kontejner**: kontejner zajišťuje seskupení sady objektů BLOB. Všechny objekty blob musí být v kontejneru. Účet může obsahovat neomezený počet kontejnerů. Kontejner můžete pojmout neomezený počet objektů blob.
* **Objekt BLOB**: soubor libovolného typu a velikosti. Existují dva typy objektů BLOB, které mohou být uloženy ve službě Azure Storage: objekty BLOB bloků a stránek. Většina souborů jsou objekty BLOB bloku. Jeden objekt blob bloku může být až do velikosti 200 GB. Tento kurz používá objekty BLOB bloku. Objekty BLOB stránky, jiný typ objektu blob může být až 1 TB, velikost a jsou efektivnější, pokud jsou často upravit rozsah bajtů v souboru. Další informace o objektech BLOB najdete v tématu [vysvětlení objektů BLOB bloku, doplňovací objekty BLOB a objekty BLOB stránky](http://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Formát adresy URL**: objekty BLOB jsou adresovatelné v následujícím formátu adresy URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Formát výše uvedené platí do veřejného cloudu Azure. Pokud používáte jiný cloud Azure, použijte koncový bod v rámci [webu Azure Portal](https://portal.azure.com) určit váš koncový bod adresy URL.)
  
    Ve výše uvedené, formátu `storageaccount` představuje název účtu úložiště `container_name` představuje název vašeho kontejneru a `blob_name` představuje název objektu blob služby v uvedeném pořadí. V rámci název kontejneru může mít několik cest oddělených lomítkem, **/**. Název kontejneru příklad v tomto kurzu byla **MyJob**, a **${sestavení\_ID} / ${sestavení\_číslo}** byl použit pro běžné virtuální cestu, což vede k nutnosti adresu URL objektu blob následující tvar:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Další postup
* [Musí splňovat Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Azure Storage SDK pro Javu](https://github.com/azure/azure-storage-java)
* [Referenční informace ke klientské sadě SDK služby Azure Storage](http://dl.windowsazure.com/storage/javadoc/)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog týmu Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)

Další informace najdete na webu [Azure pro vývojáře v Javě](/java/azure).