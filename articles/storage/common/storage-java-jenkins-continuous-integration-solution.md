---
title: Použití služby Azure Storage s řešením Jenkins kontinuální integrace
description: V tomto kurzu ukazují, jak používat službu Azure blob jako úložiště pro sestavení vytvořených řešením Jenkins kontinuální integrace artefakty.
ms.topic: article
ms.author: tarcher
author: tarcher
services: devops
custom: jenkins
ms.date: 07/31/2018
ms.component: common
ms.openlocfilehash: 040f7ef8d9b50998ac2b84ed9d4e3712f4ed5835
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246863"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Použití služby Azure Storage s řešením Jenkins kontinuální integrace

Tento článek ukazuje, jak používat úložiště objektů Blob jako úložiště artefaktů sestavení vytvořených řešením Jenkins kontinuální integrace (CI), nebo jako zdrojové soubory ke stažení pro použití v procesu sestavení. Jednou ze scénářů, ve kterém by pro vás toto řešení užitečné je, když jste psaní kódu v prostředí agilního vývoje (pomocí sad Java SDK nebo jiných jazyků), jsou sestavení spuštěna založené na průběžné integrace a potřebujete úložiště artefaktů sestavení, tak, aby může třeba je sdílet s ostatními členy organizace vašim zákazníkům nebo udržujte archiv. Další možností je úlohy sestavení samotné vyžaduje další soubory, například závislosti stáhnout jako část sestavení vstup.

V tomto kurzu budete používat modul plug-in Azure Storage pro Jenkins CI k dispozici společností Microsoft.

## <a name="jenkins-overview"></a>Přehled Jenkinse
Jenkins umožňuje průběžnou integraci softwarovém projektu umožňuje vývojářům snadno integrovat svoje změny kódu a je tvořen sestavení automaticky a často, a tím zvýšit produktivitu vývojářů. Sestavení se systémovou správou verzí a artefakty sestavení může být odeslán do různých úložišť. Tento článek ukazuje, jak používat úložiště objektů blob v Azure jako úložiště artefaktů sestavení. Zobrazí také ke stažení závislosti z úložiště objektů blob v Azure.

Další informace o systému Jenkins najdete v [splňovat Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Výhody použití služby Blob service
Výhody použití služby Blob service pro hostování artefakty sestavení agilního vývoje patří:

* Vysokou dostupnost vašich artefaktů sestavení a/nebo ke stažení závislostí.
* Výkon, pokud je vaše řešení Jenkins CI odesílá artefakty sestavení.
* Výkon, pokud je vaše zákazníky a partnery stažení artefaktů sestavení.
* Kontrolu nad zásad přístupu uživatele, s možností volby mezi anonymní přístup, na základě vypršení platnosti sdílený přístupový podpis přístup, privátní přístup, atd.

## <a name="prerequisites"></a>Požadavky
* Průběžná integrace řešení Jenkins.
  
    Pokud momentálně nemáte k dispozici řešení Jenkins CI, můžete spustit řešení Jenkins CI následujícím způsobem:
  
  1. Na počítači s podporou Javy, stáhněte si jenkins.war z <http://jenkins-ci.org>.
  2. Na příkazovém řádku, který se otevírá ve složce, která obsahuje jenkins.war spusťte:
     
      `java -jar jenkins.war`

  3. V prohlížeči otevřete `http://localhost:8080/` otevřete řídicí panel Jenkinse, který použijete k instalaci a konfiguraci modulu plug-in Azure Storage.
     
      Během typického řešení Jenkins CI by nastavit tak, aby se spouštěl jako služba, systémem Jenkins war na příkazovém řádku jsou dostatečné pro účely tohoto kurzu.
* Účet Azure. Můžete se zaregistrovat pro účet Azure na <http://www.azure.com>.
* Účet úložiště Azure. Pokud ještě nemáte účet úložiště, můžete vytvořit pomocí postupu v [vytvořit účet úložiště](../common/storage-quickstart-create-account.md).
* Znalost řešení Jenkins CI je doporučeno, avšak není nutné, protože následující obsah bude používat jako základní příklad zobrazit kroky potřebné při použití služby Blob service pro Jenkins CI jako úložiště artefaktů sestavení.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Použití služby Blob service s Jenkins CI
Použití služby Blob service s využitím Jenkinse, budete muset nainstalovat modul plug-in Azure Storage, konfigurace modulu plug-in použít svůj účet úložiště a pak vytvořte akce po sestavení, která odesílá artefakty sestavení do účtu úložiště. Tyto kroky jsou popsány v následujících částech.

## <a name="how-to-install-the-azure-storage-plugin"></a>Jak nainstalovat modul plug-in Azure Storage
1. V řídicím panelu Jenkinse vyberte **spravovat Jenkins**.
2. V **spravovat Jenkins** stránce **Správa modulů plug-in**.
3. Vyberte kartu **Available** (K dispozici).
4. V **artefaktů Nahrávajících** oddílu, zkontrolujte **modul plug-in Microsoft Azure Storage**.
5. Vyberte buď **nainstalovat bez klauzule restart** nebo **stáhnout a nainstalovat po restartování**.
6. Restartujte Jenkinse.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Jak nakonfigurovat modul plug-in Azure Storage, aby používalo účet úložiště
1. V řídicím panelu Jenkinse vyberte **spravovat Jenkins**.
2. V **spravovat Jenkins** stránce **konfigurovat systém**.
3. V **konfigurací účtu úložiště Microsoft Azure** části:
   1. Zadejte název svého účtu úložiště, který můžete získat z [webu Azure Portal](https://portal.azure.com).
   2. Zadejte klíč účtu úložiště, také dají získat od [webu Azure Portal](https://portal.azure.com).
   3. Použijte výchozí hodnotu pro **adresu URL koncového bodu služby objektů Blob** použití globálního cloudu Azure. Pokud používáte jiný cloud Azure, použijte koncový bod uvedená v [webu Azure Portal](https://portal.azure.com) pro váš účet úložiště. 
   4. Vyberte **ověřit přihlašovací údaje úložiště** k ověření svého účtu úložiště. 
   5. [Volitelné] Pokud máte další účty úložiště, které chcete zpřístupnit Jenkins CI, vyberte **přidat další účty úložiště**.
   6. Vyberte **Uložit** uložte nastavení.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Jak vytvořit akce po sestavení, která odesílá artefakty sestavení do účtu úložiště
Pro vzdělávací účely musíte nejprve vytvořit úlohu, která vytvoří několik souborů a potom přidat do akce po sestavení k nahrání souborů do účtu úložiště.

1. V řídicím panelu Jenkinse vyberte **nová položka**.
2. Název úlohy **MyJob**vyberte **sestavení softwaru volný styl projektu**a pak vyberte **OK**.
3. V **sestavení** část konfigurace úlohy, vyberte **přidat krok sestavení** a vyberte **spuštění Windows dávkového příkazu**.
4. V **příkaz**, použijte následující příkazy:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. V **akcích po sestavení** část konfigurace úlohy, vyberte **přidat akci po sestavení** a vyberte **odešlete artefakty do služby Azure Blob storage**.
6. Pro **název účtu úložiště**, vyberte účet úložiště.
7. Pro **název kontejneru**, zadejte název kontejneru. (Kontejneru se vytvoří, pokud již neexistuje při odeslání artefaktů sestavení.) Můžete použít proměnné prostředí, takže v tomto příkladu zadejte `${JOB_NAME}` jako název kontejneru.
   
    **Tip**
   
    Níže **příkaz** části, kde jste zadali skript pro **spuštění Windows dávkového příkazu** je odkaz na objekt environment variables rozpoznávaných Jenkinse. Vyberte tento odkaz a zjistěte, popisy a názvy proměnných prostředí. Proměnné prostředí, které obsahují speciální znaky, jako **BUILD_URL** proměnné prostředí, nejsou povoleny jako běžné virtuální cestu a název kontejneru.
8. Vyberte **zveřejnit nový kontejner ve výchozím nastavení** pro účely tohoto příkladu. (Pokud chcete použít kontejner privátní, bude potřeba vytvořit sdílený přístupový podpis pro povolení přístupu, který je nad rámec tohoto článku. Další informace o sdílených přístupových podpisů v [použití sdílených přístupových podpisů (SAS)](../storage-dotnet-shared-access-signature-part-1.md).)
9. [Volitelné] Vyberte **čisté kontejneru před nahráním** Pokud chcete, aby kontejneru vymazání obsahu předtím, než se nahrají artefakty sestavení (políčko nechte nezaškrtnuté Pokud nechcete vyčistit obsah kontejneru).
10. Pro **seznamu artefaktů k odeslání**, zadejte `text/*.txt`.
11. Pro **běžné virtuální cestu pro nahrané artefakty**, pro účely tohoto kurzu zadejte `${BUILD\_ID}/${BUILD\_NUMBER}`.
12. Vyberte **Uložit** uložte nastavení.
13. Na řídicím panelu Jenkinse vyberte **Build Now** spuštění **MyJob**. Prohlédněte si výstup konzoly stav. Stavové zprávy pro Azure storage se zahrnou výstup konzoly při spuštění akce po sestavení k odeslání artefaktů sestavení.
14. Po úspěšném dokončení úlohy můžete zkontrolovat artefakty sestavení tak, že otevřete veřejných objektů blob.
    1. Přihlaste se k [Portálu Azure](https://portal.azure.com).
    2. Vyberte **Úložiště**.
    3. Vyberte název účtu úložiště, který jste použili pro Jenkinse.
    4. Vyberte **kontejnery**.
    5. Vyberte kontejner s názvem **myjob**, což je malá verzi název úlohy, který jste přiřadili při vytvoření úlohy Jenkinse. Názvy kontejnerů a objektů blob názvy jsou malá písmena (a malá a velká písmena) ve službě Azure storage. V seznamu objektů blob pro kontejner s názvem **myjob**, měli byste vidět **hello.txt** a **date.txt**. Zkopírujte adresu URL pro některý z těchto položek a otevřít ji v prohlížeči. Zobrazí se textový soubor, který byl nahrán jako artefakt sestavení.

Za úlohu lze vytvořit pouze jednu akci po sestavení, která odešle artefakty do služby Azure blob storage. Jedné akce po sestavení k odeslání artefaktů do Azure blob storage můžete určit různé soubory (včetně zástupných znaků) a cesty k souborům v rámci **seznamu artefaktů k odeslání** oddělte středníky jako oddělovač. Například pokud je vaše Jenkins sestavení vytvoří soubory JAR a soubory TXT v pracovním prostoru **sestavení** složce a chcete nahrát i do Azure blob storage, použijte následující hodnotu **seznamu artefaktů k odeslání** možnost: `build/\*.jar;build/\*.txt`. Syntaxe dvěma dvojtečkami také můžete zadat cestu pro použití v rámci názvu objektu blob. Například, pokud chcete, aby kromě souborů JAR získat odeslán pomocí **binární soubory** cesta objektu blob a soubory TXT se nahrát pomocí **oznámení** v cestě objektů blob použít následující hodnotu **seznam Artefakty k nahrání** možnost: `build/\*.jar::binaries;build/\*.txt::notices`.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Jak vytvořit krok sestavení, které stáhne ze služby Azure blob storage
Následující postup ukazuje, ke konfiguraci kroku sestavení na stažení položek z Azure blob storage, což je užitečné, pokud chcete zahrnout položky ve vašem buildu. Příklad použití tohoto modelu je kromě souborů JAR, které můžete chtít zachovat ve službě Azure blob storage.

1. V **sestavení** část konfigurace úlohy, vyberte **přidat krok sestavení** a vyberte **stáhnout z úložiště objektů Blob v Azure**.
2. Pro **název účtu úložiště**, vyberte účet úložiště.
3. Pro **název kontejneru**, zadejte název kontejneru, který obsahuje objekty BLOB, kterou chcete stáhnout. Můžete použít proměnné prostředí.
4. Pro **název objektu Blob**, zadejte název objektu blob. Můžete použít proměnné prostředí. Navíc můžete použít hvězdičku, jako zástupný znak po zadání počátečního písmena názvu objektu blob. Například **projektu\***  zadáte všechny objekty BLOB, jejichž jména začínají **projektu**.
5. [Volitelné] Pro **cestu pro stažení**, zadejte cestu na počítač Jenkinse, kam chcete soubory stáhnout z úložiště objektů blob v Azure. Můžete také použít proměnné prostředí. (Pokud nezadáte hodnotu **cestu pro stažení**, stáhnou se soubory z Azure blob storage do pracovního prostoru úlohy.)

Pokud máte další položky, které chcete stáhnout z úložiště objektů blob v Azure, můžete vytvořit další kroky sestavení.

Po spuštění sestavení, můžete zkontrolovat výstup na konzole historie sestavení nebo podívejte se na vaše umístění stahování, pokud chcete zobrazit, zda byly úspěšně staženy objekty BLOB, které jste očekávali.  

## <a name="components-used-by-the-blob-service"></a>Součásti, které používají službu Blob service
Tato část obsahuje přehled komponent služby objektů Blob.

* **Účet služby Storage:** Veškerý přístup ke službě Azure Storage se provádí prostřednictvím účtu úložiště. Účet úložiště je nejvyšší úroveň oboru názvů pro přístup k objektům BLOB. Účet může obsahovat neomezený počet kontejnerů, jejich celkové velikosti je v části 100 TB.
* **Kontejner**: kontejner zajišťuje seskupení sady objektů BLOB. Všechny objekty blob musí být v kontejneru. Účet může obsahovat neomezený počet kontejnerů. Kontejner můžete pojmout neomezený počet objektů blob.
* **Objekt BLOB**: soubor libovolného typu a velikosti. Existují dva typy objektů BLOB, které mohou být uloženy ve službě Azure Storage: objekty BLOB bloků a stránek. Většina souborů jsou objekty BLOB bloku. Jeden objekt blob bloku může být až do velikosti 200 GB. Tento kurz používá objekty BLOB bloku. Objekty BLOB stránky, jiný typ objektu blob může být až 1 TB, velikost a jsou efektivnější, pokud jsou často upravit rozsah bajtů v souboru. Další informace o objektech BLOB najdete v tématu [vysvětlení objektů BLOB bloku, doplňovací objekty BLOB a objekty BLOB stránky](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Formát adresy URL**: objekty BLOB jsou adresovatelné v následujícím formátu adresy URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Formát, nahoře platí pro globální cloud Azure. Pokud používáte jiný cloud Azure, použijte koncový bod v rámci [webu Azure Portal](https://portal.azure.com) určit váš koncový bod adresy URL.)
  
    Ve výše uvedené, formátu `storageaccount` představuje název účtu úložiště `container_name` představuje název vašeho kontejneru a `blob_name` představuje název objektu blob služby v uvedeném pořadí. V rámci název kontejneru může mít několik cest oddělených lomítkem, **/**. Příklad názvu kontejneru používá pro účely tohoto kurzu byla **MyJob**, a **${sestavení\_ID} a$ {sestavení\_číslo}** byl použit pro běžné virtuální cestu, což vede k nutnosti adresy URL objektu blob v následujícím formátu:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Řešení potíží s modulem plug-in Jenkinse

Pokud v modulech plug-in Jenkinse narazíte na nějaké chyby, založte problém na stránce [Jenkins JIRA](https://issues.jenkins-ci.org/) pro konkrétní komponentu.

## <a name="next-steps"></a>Další postup
* [Musí splňovat Jenkinse](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
* [Azure Storage SDK pro Javu](https://github.com/azure/azure-storage-java)
* [Referenční informace ke klientské sadě SDK služby Azure Storage](http://dl.windowsazure.com/storage/javadoc/)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog týmu Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/)

Další informace najdete na webu [Azure pro vývojáře v Javě](/java/azure).