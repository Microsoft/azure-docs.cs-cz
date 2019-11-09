---
title: Jak používat Hudson s úložištěm objektů BLOB | Microsoft Docs
description: Popisuje, jak používat Hudson s úložištěm objektů BLOB v Azure jako úložiště pro artefakty sestavení.
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 08/13/2019
ms.author: tarcher
ms.subservice: common
ms.openlocfilehash: 75b0ea106be04cd77b18bfed8487edb6a7b7f65b
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839186"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Použití Azure Storage s řešením Hudson Continuous Integration
## <a name="overview"></a>Přehled
Následující informace ukazují, jak používat úložiště objektů BLOB jako úložiště artefaktů sestavení vytvořeného řešením Hudson Continuous Integration (CI) nebo jako zdroj souborů ke stažení, které se mají použít v procesu sestavení. Jeden z scénářů, kde byste to poznamenali, je při kódování v agilním vývojovém prostředí (pomocí jazyka Java nebo jiných jazyků) jsou sestavení spuštěná v závislosti na průběžné integraci a potřebujete úložiště pro vaše artefakty sestavení, abyste mohli, můžete je například sdílet s ostatními členy organizace, vašimi zákazníky nebo uchovávat archivy.  Dalším scénářem je, že vlastní úloha sestavení vyžaduje jiné soubory, například závislosti, které se mají stáhnout jako součást vstupu buildu.

V tomto kurzu budete používat modul plug-in Azure Storage pro Hudson CI zpřístupněný Microsoftem.

## <a name="introduction-to-hudson"></a>Úvod do Hudson
Hudson umožňuje průběžnou integraci softwarového projektu tím, že umožňuje vývojářům snadno integrovat změny kódu a vytvářet buildy automaticky a často, což zvyšuje produktivitu vývojářů. Sestavení jsou verze a artefakty sestavení lze nahrát do různých úložišť. V tomto článku se dozvíte, jak používat úložiště objektů BLOB v Azure jako úložiště artefaktů sestavení. Také se dozvíte, jak stahovat závislosti z úložiště objektů BLOB v Azure.

Další informace o Hudson najdete v závislosti na [Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Výhody použití Blob service
Výhody použití Blob service k hostování artefaktů sestavení agilního vývoje zahrnují:

* Vysoká dostupnost artefaktů sestavení nebo závislostí ke stažení.
* Výkon, když řešení CI Hudson nahraje artefakty sestavení.
* Výkon, když vaši zákazníci a partneři stáhnou vaše artefakty sestavení.
* Kontrola nad zásadami přístupu uživatele s volbou mezi anonymním přístupem, přístupem ke sdíleným podpisovým podpisem založeným na vypršení platnosti, privátním přístupem atd.

## <a name="prerequisites"></a>Požadavky
K použití Blob service s řešením CI v Hudson budete potřebovat následující:

* Řešení Hudson Continuous Integration.
  
    Pokud v současné době nemáte řešení CI Hudson, můžete řešení CI Hudson spustit pomocí následující techniky:
  
  1. Na počítači s podporou Java Stáhněte Hudson WAR z <http://hudson-ci.org/>.
  2. Na příkazovém řádku, který je otevřen do složky, která obsahuje Hudson WAR, spusťte Hudson WAR. Pokud jste například stáhli verzi 3.1.2:
     
      `java -jar hudson-3.1.2.war`

  3. V prohlížeči otevřete `http://localhost:8080/`. Otevře se řídicí panel Hudson.
  4. Při prvním použití Hudson dokončete počáteční nastavení v `http://localhost:8080/`.
  5. Po dokončení počátečního nastavení zrušte spuštěnou instanci Hudson WAR, znovu spusťte Hudson WAR a znovu otevřete řídicí `http://localhost:8080/`panel Hudson, který budete používat k instalaci a konfiguraci modulu plug-in Azure Storage.
     
      I když by bylo typické řešení CI Hudson nastaveno tak, aby běželo jako služba, bude pro tento kurz stačit spuštění Hudson War na příkazovém řádku.
* Účet Azure. Účet Azure si můžete zaregistrovat na <https://www.azure.com>.
* Účet úložiště Azure. Pokud ještě nemáte účet úložiště, můžete ho vytvořit pomocí postupu v části [Vytvoření účtu úložiště](../common/storage-quickstart-create-account.md).
* Řešení Hudson CI se doporučuje, ale není nutné, protože následující obsah bude používat základní příklad, který vám ukáže, jaké kroky potřebujete při použití Blob service jako úložiště pro artefakty sestavení Hudson CI.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Použití Blob service s Hudson CI
Pokud chcete použít Blob service s Hudson, budete muset nainstalovat modul plug-in Azure Storage, nakonfigurovat modul plug-in tak, aby používal váš účet úložiště, a pak vytvořit akci po sestavení, která nahraje artefakty sestavení do vašeho účtu úložiště. Tyto kroky jsou popsány v následujících částech.

## <a name="how-to-install-the-azure-storage-plugin"></a>Jak nainstalovat modul plug-in Azure Storage
1. V řídicím panelu Hudson klikněte na **Spravovat Hudson**.
2. Na stránce **Spravovat Hudson** klikněte na **Spravovat moduly plug-in**.
3. Klikněte na kartu **dostupné** .
4. Klikněte na **Další**.
5. V části pro **nahrávání artefaktů** vyberte **Microsoft Azure Storage modul plug-in**.
6. Klikněte na **Instalovat**.
7. Po dokončení instalace restartujte Hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Jak nakonfigurovat modul plug-in Azure Storage pro použití vašeho účtu úložiště
1. V řídicím panelu Hudson klikněte na **Spravovat Hudson**.
2. Na stránce **Spravovat Hudson** klikněte na **Konfigurovat systém**.
3. V části **Konfigurace účtu Microsoft Azure Storage** :
   
    a. Zadejte název svého účtu úložiště, který můžete získat z [Azure Portal](https://portal.azure.com).
   
    b. Zadejte svůj klíč účtu úložiště, který je také možné získat z [Azure Portal](https://portal.azure.com).
   
    c. Pokud používáte globální cloud Azure, použijte výchozí hodnotu pro **adresu URL koncového bodu služby BLOB Service** . Pokud používáte jiný cloud Azure, použijte koncový bod zadaný v [Azure Portal](https://portal.azure.com) pro váš účet úložiště.
   
    d. Kliknutím na **ověřit přihlašovací údaje úložiště** ověřte účet úložiště.
   
    e. Volitelné Pokud máte další účty úložiště, které chcete zpřístupnit pro Hudson CI, klikněte na **Přidat další účty úložiště**.
   
    f. Uložte nastavení kliknutím na **Uložit** .

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Jak vytvořit akci po sestavení, která odešle artefakty sestavení do vašeho účtu úložiště
Pro účely instrukcí nejdřív budeme muset vytvořit úlohu, která vytvoří několik souborů, a pak do akce po sestavení přidat soubory k nahrání souborů do účtu úložiště.

1. V řídicím panelu Hudson klikněte na **Nová úloha**.
2. Pojmenujte úlohu **MyJob**, klikněte na **vytvořit bezplatnou softwarovou úlohu**a pak klikněte na **OK**.
3. V části **sestavení** v konfiguraci úlohy klikněte na **Přidat krok sestavení** a vyberte **příkaz Spustit dávku Windows**.
4. V **příkazu**použijte následující příkazy:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. V části **akce po sestavení** v konfiguraci úlohy klikněte na **Odeslat artefakty a Microsoft Azure úložiště objektů BLOB**.
6. V poli **název účtu úložiště**vyberte účet úložiště, který se má použít.
7. Jako **název kontejneru**zadejte název kontejneru. (Kontejner bude vytvořen, pokud ještě neexistuje, když jsou nahrány artefakty sestavení.) Můžete použít proměnné prostředí, takže v tomto příkladu jako název kontejneru zadejte **$ {JOB_NAME}** .
   
    **Tip**
   
    Pod oddílem **příkazu** , kde jste zadali skript pro **příkaz Spustit dávku Windows** , je odkaz na proměnné prostředí rozpoznané nástrojem Hudson. Kliknutím na tento odkaz získáte informace o názvech a popisech proměnných prostředí. Proměnné prostředí, které obsahují speciální znaky, jako je například proměnná prostředí **BUILD_URL** , nejsou povoleny jako název kontejneru nebo běžná virtuální cesta.
8. V tomto příkladu klikněte na **nastavit nový kontejner jako Public** . (Pokud chcete použít privátní kontejner, budete muset pro povolení přístupu vytvořit sdílený přístupový podpis. Mimo rámec tohoto článku. Další informace o podpisech sdíleného přístupu najdete v [používání sdílených přístupových podpisů (SAS)](storage-sas-overview.md).)
9. Volitelné Klikněte na tlačítko **vyčistit kontejner před odesláním** , pokud chcete, aby kontejner vymazal obsah před odesláním artefaktů sestavení (nechte nezaškrtnuté, pokud nechcete vyčistit obsah kontejneru).
10. **Seznam artefaktů, které se mají nahrát**, získáte zadáním **textu/*. txt**.
11. Pro **společnou virtuální cestu pro nahrané artefakty**zadejte **$ {Build\_ID}/$ {Build\_Number}** .
12. Uložte nastavení kliknutím na **Uložit** .
13. V řídicím panelu Hudson klikněte na **sestavit** a spusťte **MyJob**. Projděte si výstup konzoly pro stav. Stavové zprávy pro Azure Storage budou zahrnuty do výstupu konzoly, když akce po sestavení začne nahrávat artefakty sestavení.
14. Po úspěšném dokončení úlohy můžete artefakty sestavení prošetřit otevřením veřejného objektu BLOB.
    
    a. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
    
    b. Klikněte na **úložiště**.
    
    c. Klikněte na název účtu úložiště, který jste použili pro Hudson.
    
    d. Klikněte na **kontejnery**.
    
    e. Klikněte na kontejner s názvem **MYJOB**, což je malá nebo Velká verze názvu úlohy, kterou jste přiřadili při vytváření úlohy Hudson. Názvy kontejnerů a názvy objektů BLOB jsou malými písmeny (a rozlišuje velká a malá písmena) v Azure Storage. V seznamu objektů BLOB pro kontejner s názvem **MYJOB** byste měli vidět **Hello. txt** a **Date. txt**. Zkopírujte adresu URL některé z těchto položek a otevřete ji v prohlížeči. Zobrazí se textový soubor, který byl nahrán jako artefakt sestavení.

Pro každou úlohu se dá vytvořit jenom jedna akce po sestavení, která nahrává artefakty do úložiště objektů BLOB v Azure. Jedna akce po sestavení pro nahrání artefaktů do úložiště objektů BLOB v Azure může určovat různé soubory (včetně zástupných znaků) a cesty k souborům v **seznamu artefaktů, které se budou nahrávat** středníkem jako oddělovač. Například pokud vaše sestavení Hudson vytváří soubory. TXT a soubory TXT ve složce **sestavení** pracovního prostoru a chcete je nahrát do úložiště objektů BLOB v Azure, použijte následující **seznam artefaktů pro nahrání** hodnoty: **Build/\*. jar; Build/\*. txt**. K určení cesty k použití v názvu objektu blob můžete použít také syntaxi typu Double-dvojtečka. Například pokud chcete, aby se jar nahrál pomocí **binárních** souborů v cestě objektů BLOB a souborů txt, aby se nahrály pomocí **oznámení** v cestě objektu blob, použijte následující **seznam artefaktů pro nahrání** hodnoty: **Build/\*. jar:: binární soubory, Build/\*. txt:: oznámení**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Postup vytvoření kroku sestavení, který se stáhne ze služby Azure Blob Storage
Následující kroky ukazují, jak nakonfigurovat krok sestavení ke stažení položek z úložiště objektů BLOB v Azure. To je užitečné, pokud chcete zahrnout položky do sestavení, například jar, které zachováte v úložišti objektů BLOB v Azure.

1. V části **sestavení** v konfiguraci úlohy klikněte na **Přidat krok sestavení** a vyberte **stáhnout ze služby Azure Blob Storage**.
2. V poli **název účtu úložiště**vyberte účet úložiště, který se má použít.
3. Jako **název kontejneru**zadejte název kontejneru, který obsahuje objekty blob, které chcete stáhnout. Můžete použít proměnné prostředí.
4. Jako **název objektu BLOB**zadejte název objektu BLOB. Můžete použít proměnné prostředí. Můžete také použít hvězdičku jako zástupný znak po zadání počátečních písmen (ů) názvu objektu BLOB. Například **projekt\\** * by určoval všechny objekty blob, jejichž názvy začínají na **Project**.
5. Volitelné Do pole **cesta ke stažení**zadejte cestu k Hudson počítači, kde chcete stahovat soubory z úložiště objektů BLOB v Azure. Lze také použít proměnné prostředí. (Pokud nezadáte hodnotu pro **cestu ke stažení**, soubory z úložiště objektů BLOB v Azure se stáhnou do pracovního prostoru úlohy.)

Pokud máte další položky, které chcete stáhnout z úložiště objektů BLOB v Azure, můžete vytvořit další kroky sestavení.

Po spuštění sestavení můžete zkontrolovat výstup konzoly historie sestavení nebo se podívat na umístění pro stahování, abyste viděli, zda byly objekty blob, které jste očekávali, úspěšně stáhly.

## <a name="components-used-by-the-blob-service"></a>Součásti používané Blob service
V následující části najdete přehled komponent Blob service.

* **Účet úložiště**: veškerý přístup k Azure Storage se provádí prostřednictvím účtu úložiště. Toto je nejvyšší úroveň oboru názvů pro přístup k objektům blob. Účet může obsahovat neomezený počet kontejnerů, pokud je jeho celková velikost menší než 100 TB.
* **Kontejner**: kontejner poskytuje seskupení sady objektů BLOB. Všechny objekty blob musí být v kontejneru. Účet může obsahovat neomezený počet kontejnerů. Kontejner můžete pojmout neomezený počet objektů blob.
* **Objekt BLOB**: soubor libovolného typu a velikosti. Existují dva typy objektů blob, které mohou být uloženy v Azure Storage: objekty blob bloku a stránky. Většina souborů je objekty blob bloku. Jeden objekt blob bloku může mít velikost až 200 GB. Tento kurz používá objekty blob bloku. Objekty blob stránky, jiný typ objektu blob, můžou mít velikost až 1 TB a jsou efektivnější, pokud se často upravují rozsahy bajtů v souboru. Další informace o objektech blob najdete v tématu [Principy objektů blob bloku, doplňovacích objektů BLOB a objektů blob stránky](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Formát adresy URL**: objekty blob jsou adresovatelné v následujícím formátu adresy URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Výše uvedený formát se vztahuje na globální cloud Azure. Pokud používáte jiný cloud Azure, určete koncový bod adresy URL pomocí koncového bodu v rámci [Azure Portal](https://portal.azure.com) .)
  
    Ve výše uvedeném formátu `storageaccount` představuje název vašeho účtu úložiště, `container_name` představuje název vašeho kontejneru a `blob_name` představuje název objektu blob, v uvedeném pořadí. V rámci názvu kontejneru můžete mít více cest oddělených lomítkem, **/** . Vzorový název kontejneru v tomto kurzu byl **MyJob**a pro běžnou virtuální cestu se použilo **$ {Build\_ID}/$ {Build\_}** . Výsledkem je, že objekt BLOB má adresu URL následujícího formátu:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Další kroky
* [Splnění Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Azure Storage SDK pro jazyk Java](https://github.com/azure/azure-storage-java)
* [Referenční informace ke klientské sadě SDK služby Azure Storage](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog týmu Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/)

Další informace najdete na webu [Azure pro vývojáře v Javě](/java/azure).
