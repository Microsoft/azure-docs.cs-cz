---
title: Použití Azure Storage s řešením Jenkinse Continuous Integration
description: V tomto kurzu se dozvíte, jak používat službu Azure Blob Service jako úložiště pro artefakty sestavení vytvořené řešením Jenkinse Continuous Integration.
keywords: Jenkinse, Azure, DevOps, Storage, cicd
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: df1d59c40fd09fb055db9d7622d86ff9c82991b8
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624680"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Použití Azure Storage s řešením Jenkinse Continuous Integration

Tento článek ukazuje, jak používat úložiště objektů BLOB jako úložiště artefaktů sestavení vytvořených řešením Jenkinse Continuous Integration (CI), nebo jako zdroj souborů ke stažení, které se mají použít v procesu sestavení. Jeden z scénářů, kde by toto řešení bylo užitečné, je při kódování v agilním vývojovém prostředí (pomocí jazyka Java nebo jiných jazyků), sestavení běží na základě průběžné integrace a potřebujete úložiště artefaktů sestavení, takže mohli byste je například sdílet s ostatními členy organizace, vašimi zákazníky nebo uchovávat archivy. Dalším scénářem je, že vlastní úloha sestavení vyžaduje jiné soubory, například závislosti, které se mají stáhnout jako součást vstupu buildu.

V tomto kurzu budete používat modul plug-in Azure Storage pro Jenkinse CI zpřístupněný Microsoftem.

## <a name="jenkins-overview"></a>Jenkinse – přehled

Jenkinse umožňuje průběžnou integraci softwarového projektu tím, že umožňuje vývojářům snadno integrovat změny kódu a vytvářet buildy automaticky a často, což zvyšuje produktivitu vývojářů. Sestavení jsou verze a artefakty sestavení lze nahrát do různých úložišť. Tento článek ukazuje, jak používat úložiště objektů BLOB v Azure jako úložiště artefaktů sestavení. Také se dozvíte, jak stahovat závislosti z úložiště objektů BLOB v Azure.

Další informace o Jenkinse najdete v závislosti na [Jenkinse](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Výhody použití Blob service

Výhody použití Blob service k hostování artefaktů sestavení agilního vývoje zahrnují:

* Vysoká dostupnost artefaktů sestavení nebo závislostí ke stažení.
* Výkon, když řešení CI Jenkinse nahraje artefakty sestavení.
* Výkon, když vaši zákazníci a partneři stáhnou vaše artefakty sestavení.
* Kontrola nad zásadami přístupu uživatele s volbou mezi anonymním přístupem, přístupem ke sdíleným podpisovým podpisem založeným na vypršení platnosti, privátním přístupem atd.

## <a name="prerequisites"></a>Požadavky

* Řešení Jenkinse Continuous Integration.
  
    Pokud v současné době nemáte řešení CI Jenkinse, můžete řešení CI Jenkinse spustit pomocí následující techniky:
  
  1. Na počítači s podporou jazyka Java Stáhněte Jenkinse. War z <https://jenkins-ci.org>.
  2. Na příkazovém řádku, který je otevřen do složky, která obsahuje Jenkinse. War, spusťte příkaz:
     
      `java -jar jenkins.war`

  3. V prohlížeči otevřete `http://localhost:8080/` a otevřete řídicí panel Jenkinse, který budete používat k instalaci a konfiguraci modulu plug-in Azure Storage.
     
      I když by bylo typické řešení CI Jenkinse nastaveno tak, aby běželo jako služba, bude pro tento kurz stačit spuštění Jenkinse War na příkazovém řádku.
* Účet Azure. Účet Azure si můžete zaregistrovat na <https://www.azure.com>.
* Účet úložiště Azure. Pokud ještě nemáte účet úložiště, můžete ho vytvořit pomocí postupu v části [Vytvoření účtu úložiště](../storage/common/storage-account-create.md).
* Řešení Jenkinse CI se doporučuje, ale není nutné, protože následující obsah bude používat základní příklad, který vám ukáže, jaké kroky potřebujete při použití Blob service jako úložiště pro artefakty sestavení Jenkinse CI.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Použití Blob service s Jenkinse CI
Pokud chcete použít Blob service s Jenkinse, budete muset nainstalovat modul plug-in Azure Storage, nakonfigurovat modul plug-in tak, aby používal váš účet úložiště, a pak vytvořit akci po sestavení, která nahraje artefakty sestavení do vašeho účtu úložiště. Tyto kroky jsou popsány v následujících částech.

## <a name="how-to-install-the-azure-storage-plugin"></a>Jak nainstalovat modul plug-in Azure Storage
1. V řídicím panelu Jenkinse vyberte **Spravovat Jenkinse**.
2. Na stránce **Spravovat Jenkinse** vyberte **Spravovat moduly plug-in**.
3. Vyberte kartu **Available** (K dispozici).
4. V části pro **odesílání artefaktů** se podívejte **Microsoft Azure Storage modul plug-in**.
5. Vyberte možnost **instalovat bez restartování** nebo **Stáhnout a nainstalovat po restartu**.
6. Restartujte Jenkinse.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Jak nakonfigurovat modul plug-in Azure Storage pro použití vašeho účtu úložiště
1. V řídicím panelu Jenkinse vyberte **Spravovat Jenkinse**.
2. Na stránce **Spravovat Jenkinse** vyberte **Konfigurovat systém**.
3. V části **Konfigurace účtu Microsoft Azure Storage** :
   1. Zadejte název svého účtu úložiště, který můžete získat z [Azure Portal](https://portal.azure.com).
   2. Zadejte svůj klíč účtu úložiště, který je také možné získat z [Azure Portal](https://portal.azure.com).
   3. Pokud používáte globální cloud Azure, použijte výchozí hodnotu pro **adresu URL koncového bodu služby BLOB Service** . Pokud používáte jiný cloud Azure, použijte koncový bod zadaný v [Azure Portal](https://portal.azure.com) pro váš účet úložiště. 
   4. Vyberte **ověřit přihlašovací údaje úložiště** a ověřte si účet úložiště. 
   5. Volitelné Pokud máte další účty úložiště, které chcete zpřístupnit pro Jenkinse CI, vyberte **Přidat další účty úložiště**.
   6. Vyberte **Uložit** a uložte nastavení.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Jak vytvořit akci po sestavení, která odešle artefakty sestavení do vašeho účtu úložiště
Pro instruktážní účely musíte nejprve vytvořit úlohu, která vytvoří několik souborů, a pak přidat do akce po sestavení pro nahrání souborů do účtu úložiště.

1. V řídicím panelu Jenkinse vyberte **Nová položka**.
2. Pojmenujte úlohu **MyJob**, vyberte **sestavit bezplatný softwarový projekt**a pak vyberte **OK**.
3. V části **sestavení** v konfiguraci úlohy vyberte **Přidat krok sestavení** a vyberte **příkaz Spustit dávku Windows**.
4. V **příkazu**použijte následující příkazy:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. V části **akce po sestavení** v konfiguraci úlohy vyberte **přidat akci po sestavení** a vyberte **Odeslat artefakty do úložiště objektů BLOB v Azure**.
6. V poli **název účtu úložiště**vyberte účet úložiště, který se má použít.
7. Jako **název kontejneru**zadejte název kontejneru. (Kontejner bude vytvořen, pokud ještě neexistuje, když jsou nahrány artefakty sestavení.) Můžete použít proměnné prostředí, takže pro tento příklad zadejte jako název kontejneru `${JOB_NAME}`.
   
    **Tip**
   
    Pod oddílem **příkazu** , kde jste zadali skript pro **příkaz Spustit dávku Windows** , je odkaz na proměnné prostředí rozpoznané nástrojem Jenkinse. Kliknutím na tento odkaz získáte informace o názvech a popisech proměnných prostředí. Proměnné prostředí, které obsahují speciální znaky, jako je například proměnná prostředí **BUILD_URL** , nejsou povoleny jako název kontejneru nebo běžná virtuální cesta.
8. V tomto příkladu vyberte **nastavit nový kontejner jako Public** . (Pokud chcete použít privátní kontejner, budete muset vytvořit sdílený přístupový podpis, který umožní přístup, což je nad rámec tohoto článku. Další informace o podpisech sdíleného přístupu najdete v [používání sdílených přístupových podpisů (SAS)](../storage/common/storage-sas-overview.md).)
9. Volitelné Před **odesláním vyberte vyčistit kontejner** , pokud chcete, aby kontejner vymazal obsah před odesláním artefaktů sestavení (Pokud nechcete vyčistit obsah kontejneru, nechejte nezaškrtnuté).
10. **Do seznamu artefaktů, které se mají nahrát**, zadejte `text/*.txt`.
11. Pro účely tohoto kurzu zadejte v případě **běžné virtuální cesty pro nahrané artefakty**`${BUILD\_ID}/${BUILD\_NUMBER}`.
12. Vyberte **Uložit** a uložte nastavení.
13. V řídicím panelu Jenkinse vyberte **vytvořit** a spusťte **MyJob**. Projděte si výstup konzoly pro stav. Stavové zprávy pro Azure Storage budou zahrnuty do výstupu konzoly, když akce po sestavení začne nahrávat artefakty sestavení.
14. Po úspěšném dokončení úlohy můžete artefakty sestavení prošetřit otevřením veřejného objektu BLOB.
    1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
    2. Vyberte **Úložiště**.
    3. Vyberte název účtu úložiště, který jste použili pro Jenkinse.
    4. Vyberte **kontejnery**.
    5. Vyberte kontejner s názvem **MYJOB**, což je malá a Velká verze názvu úlohy, kterou jste přiřadili při vytváření úlohy Jenkinse. Názvy kontejnerů a názvy objektů BLOB jsou malá a velká písmena (rozlišují se malá a velká písmena) ve službě Azure Storage. V seznamu objektů BLOB pro kontejner s názvem **MYJOB**byste měli vidět **Hello. txt** a **Date. txt**. Zkopírujte adresu URL některé z těchto položek a otevřete ji v prohlížeči. Zobrazí se textový soubor, který byl nahrán jako artefakt sestavení.

Pro každou úlohu se dá vytvořit jenom jedna akce po sestavení, která nahrává artefakty do úložiště objektů BLOB v Azure. Jedna akce po sestavení pro nahrání artefaktů do úložiště objektů BLOB v Azure může určovat různé soubory (včetně zástupných znaků) a cesty k souborům v **seznamu artefaktů, které se budou nahrávat** středníkem jako oddělovač. Například pokud vaše sestavení Jenkinse vytvoří soubory JAR a soubory TXT ve složce **sestavení** pracovního prostoru a chcete je nahrát do úložiště objektů BLOB v Azure, použijte následující hodnotu pro **seznam artefaktů pro nahrání** možnosti: `build/\*.jar;build/\*.txt`. K určení cesty k použití v názvu objektu blob můžete použít také syntaxi typu Double-dvojtečka. Pokud třeba chcete, aby se jar nahrály pomocí **binárních** souborů v cestě objektu BLOB a soubory TXT, které se nahrály pomocí **oznámení** v cestě objektu blob, použijte následující hodnotu, kterou se zobrazí v **seznamu artefaktů pro nahrání** možnosti: `build/\*.jar::binaries;build/\*.txt::notices`.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Postup vytvoření kroku sestavení, který se stáhne ze služby Azure Blob Storage
Následující postup ukazuje, jak nakonfigurovat krok sestavení pro stažení položek z úložiště objektů BLOB v Azure, což je užitečné, pokud chcete zahrnout položky do sestavení. Příkladem použití tohoto modelu je jar, který je možné zachovat v úložišti objektů BLOB v Azure.

1. V části **sestavení** v konfiguraci úlohy vyberte **Přidat krok sestavení** a vyberte **stáhnout ze služby Azure Blob Storage**.
2. V poli **název účtu úložiště**vyberte účet úložiště, který se má použít.
3. Jako **název kontejneru**zadejte název kontejneru, který obsahuje objekty blob, které chcete stáhnout. Můžete použít proměnné prostředí.
4. Jako **název objektu BLOB**zadejte název objektu BLOB. Můžete použít proměnné prostředí. Můžete také použít hvězdičku jako zástupný znak po zadání počátečních písmen (ů) názvu objektu BLOB. Například **projekt\\** * by určoval všechny objekty blob, jejichž názvy začínají na **Project**.
5. Volitelné Do pole **cesta ke stažení**zadejte cestu k Jenkinse počítači, kde chcete stahovat soubory z úložiště objektů BLOB v Azure. Lze také použít proměnné prostředí. (Pokud nezadáte hodnotu pro **cestu ke stažení**, soubory z úložiště objektů BLOB v Azure se stáhnou do pracovního prostoru úlohy.)

Pokud máte další položky, které chcete stáhnout z úložiště objektů BLOB v Azure, můžete vytvořit další kroky sestavení.

Po spuštění sestavení můžete zkontrolovat výstup konzoly historie sestavení nebo se podívat na umístění pro stahování, abyste viděli, zda byly objekty blob, které jste očekávali, úspěšně stáhly.  

## <a name="components-used-by-the-blob-service"></a>Součásti používané Blob service
Tato část poskytuje přehled komponent Blob service.

* **Účet služby Storage:** Veškerý přístup ke službě Azure Storage se provádí prostřednictvím účtu úložiště. Účet úložiště je nejvyšší úroveň oboru názvů pro přístup k objektům blob. Účet může obsahovat neomezený počet kontejnerů, pokud je jeho celková velikost menší než 100 TB.
* **Kontejner**: kontejner poskytuje seskupení sady objektů BLOB. Všechny objekty blob musí být v kontejneru. Účet může obsahovat neomezený počet kontejnerů. Kontejner můžete pojmout neomezený počet objektů blob.
* **Objekt BLOB**: soubor libovolného typu a velikosti. Existují dva typy objektů blob, které mohou být uloženy v Azure Storage: objekty blob bloku a stránky. Většina souborů je objekty blob bloku. Jeden objekt blob bloku může mít velikost až 200 GB. Tento kurz používá objekty blob bloku. Objekty blob stránky, jiný typ objektu blob, můžou mít velikost až 1 TB a jsou efektivnější, pokud se často upravují rozsahy bajtů v souboru. Další informace o objektech blob najdete v tématu [Principy objektů blob bloku, doplňovacích objektů BLOB a objektů blob stránky](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Formát adresy URL**: objekty blob jsou adresovatelné v následujícím formátu adresy URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Výše uvedený formát se vztahuje na globální cloud Azure. Pokud používáte jiný cloud Azure, určete koncový bod adresy URL pomocí koncového bodu v rámci [Azure Portal](https://portal.azure.com) .)
  
    Ve výše uvedeném formátu `storageaccount` představuje název vašeho účtu úložiště, `container_name` představuje název vašeho kontejneru a `blob_name` představuje název objektu blob, v uvedeném pořadí. V rámci názvu kontejneru můžete mít více cest oddělených lomítkem, **/** . Vzorový název kontejneru použitý pro tento kurz byl **MyJob**a pro běžnou virtuální cestu se použilo **$ {Build\_ID}/$ {Build\_Number}** . Výsledkem je, že objekt BLOB má adresu URL následujícího formátu:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Řešení potíží s modulem plug-in Jenkinse

Pokud v modulech plug-in Jenkinse narazíte na nějaké chyby, založte problém na stránce [Jenkins JIRA](https://issues.jenkins-ci.org/) pro konkrétní komponentu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Jenkins v Azure](/azure/Jenkins/)