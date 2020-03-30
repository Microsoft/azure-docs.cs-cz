---
title: Jak používat Hudson s úložištěm objektů Blob | Dokumenty společnosti Microsoft
description: Popisuje, jak používat Hudson s úložištěm objektů blob Azure jako úložiště pro artefakty sestavení.
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 08/13/2019
ms.author: tarcher
ms.subservice: common
ms.openlocfilehash: a89439f49dd53f09d5cd40be0bf2e4981e9235d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201381"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Používání Azure Storage s řešením pro průběžnou integraci hudsonu
## <a name="overview"></a>Přehled
Následující informace ukazují, jak používat úložiště objektů Blob jako úložiště artefaktů sestavení vytvořených řešením průběžné integrace Hudson (CI) nebo jako zdroj souborů ke stažení, které mají být použity v procesu sestavení. Jedním ze scénářů, kde byste to považovali za užitečné, je, když kódujete v agilním vývojovém prostředí (pomocí jazyka Java nebo jiných jazyků), sestavení jsou spuštěna na základě průběžné integrace a potřebujete úložiště pro artefakty sestavení, takže můžete, můžete je například sdílet s ostatními členy organizace, se zákazníky nebo udržovat archiv.  Jiný scénář je, když vaše úloha sestavení sama o sobě vyžaduje další soubory, například závislosti ke stažení jako součást vstupu sestavení.

V tomto kurzu budete používat plugin Azure Storage pro Hudson CI zpřístupněný společností Microsoft.

## <a name="introduction-to-hudson"></a>Úvod do Hudsonu
Hudson umožňuje průběžnou integraci softwarového projektu tím, že umožňuje vývojářům snadno integrovat své změny kódu a mají sestavení vyrobené automaticky a často, čímž se zvyšuje produktivita vývojářů. Sestavení jsou verzí a artefakty sestavení lze nahrát do různých úložišť. Tento článek ukáže, jak používat úložiště objektů blob Azure jako úložiště artefaktů sestavení. Také ukáže, jak stahovat závislosti z úložiště objektů blob Azure.

Více informací o Hudson lze nalézt na [Meet Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Výhody použití služby Blob
Mezi výhody použití služby Blob k hostování artefaktů agilního vývojového sestavení patří:

* Vysoká dostupnost artefaktů sestavení a nebo závislostí ke stažení.
* Výkon, když vaše řešení Hudson CI nahraje artefakty sestavení.
* Výkon, když vaši zákazníci a partneři stáhnout artefakty sestavení.
* Kontrola nad zásadami přístupu uživatelů s možností volby mezi anonymním přístupem, přístupem k sdílenému přístupu založeným na vypršení platnosti, soukromým přístupem atd.

## <a name="prerequisites"></a>Požadavky
Budete potřebovat následující použití služby Blob s řešením Hudson CI:

* Řešení pro průběžnou integraci hudsonské společnosti.
  
    Pokud v současné době nemáte řešení Hudson CI, můžete spustit řešení Hudson CI pomocí následující techniky:
  
  1. Na počítači s podporou Javy [si stáhněte soubor Hudson WAR](https://www.eclipse.org/hudson/download.php).
  2. Na příkazovém řádku, který je otevřen do složky, která obsahuje Hudson WAR, spusťte Hudson WAR. Pokud jste například stáhli verzi 3.1.2:
     
      `java -jar hudson-3.1.2.war`

  3. V prohlížeči `http://localhost:8080/`otevřete . Tohle otevře hudsonskou palubní desku.
  4. Při prvním použití hudsonu dokončete počáteční nastavení na adrese `http://localhost:8080/`.
  5. Po dokončení počátečního nastavení zrušte spuštěnou instanci Hudson WAR, znovu spusťte `http://localhost:8080/`Hudson WAR a znovu otevřete řídicí panel Hudson , který použijete k instalaci a konfiguraci pluginu Azure Storage.
     
      Zatímco typické řešení Hudson CI by bylo nastaveno tak, aby běželo jako služba, spuštění války Hudson na příkazovém řádku bude pro tento kurz stačit.
* Účet Azure. Účet Azure si můžete zaregistrovat na adrese <https://www.azure.com>.
* Účet úložiště Azure. Pokud ještě nemáte účet úložiště, můžete si ho vytvořit pomocí kroků na [jdete vytvořit účet úložiště](../common/storage-account-create.md).
* Znalost řešení Hudson CI se doporučuje, ale není vyžadováno, protože následující obsah použije základní příklad, který vám ukáže kroky potřebné při použití služby Blob jako úložiště pro artefakty sestavení Hudson CI.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Jak používat službu Blob s Hudson CI
Pokud chcete použít službu Blob s Hudsonem, budete muset nainstalovat plugin Azure Storage, nakonfigurovat plugin tak, aby používal váš účet úložiště, a pak vytvořit akci po sestavení, která nahraje artefakty sestavení do vašeho účtu úložiště. Tyto kroky jsou popsány v následujících částech.

## <a name="how-to-install-the-azure-storage-plugin"></a>Jak nainstalovat plugin azure storage
1. Na řídicím panelu Hudson klikněte na **Spravovat Hudson**.
2. Na stránce **Manage Hudson** klikněte na **Manage Plugins**.
3. Klikněte na kartu **Dostupné.**
4. Klepněte na tlačítko **Ostatní**.
5. V části **Zasazovači artefaktů** vyberte **modul plug-in úložiště Microsoft Azure**.
6. Klepněte na tlačítko **Instalovat**.
7. Po dokončení instalace restartujte hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Jak nakonfigurovat plugin Azure Storage tak, aby používal váš účet úložiště
1. Na řídicím panelu Hudson klikněte na **Spravovat Hudson**.
2. Na stránce **Manage Hudson** klepněte na tlačítko **Konfigurovat systém**.
3. V části **Konfigurace účtu úložiště Microsoft Azure:**
   
    a. Zadejte název účtu úložiště, který můžete získat z [webu Azure Portal](https://portal.azure.com).
   
    b. Zadejte klíč účtu úložiště, který lze získat také z [portálu Azure](https://portal.azure.com).
   
    c. Pokud používáte globální cloud Azure, použijte výchozí hodnotu **pro adresu URL koncového bodu služby Blob** Service. Pokud používáte jiný cloud Azure, použijte koncový bod, jak je uvedeno na [portálu Azure](https://portal.azure.com) pro váš účet úložiště.
   
    d. Kliknutím na **Ověřit přihlašovací údaje úložiště** ověřte svůj účet úložiště.
   
    e. [Nepovinné] Pokud máte další účty úložiště, které chcete zpřístupnit vaší ci, klikněte na **Přidat další účty úložiště**.
   
    f. Kliknutím na **Uložit** nastavení uložte.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Jak vytvořit akci po sestavení, která nahraje artefakty sestavení do vašeho účtu úložiště
Pro účely instrukcí nejprve budeme muset vytvořit úlohu, která vytvoří několik souborů, a pak přidat do akce po sestavení, abychom soubory nahráli do účtu úložiště.

1. V řídicím panelu Hudson klepněte na tlačítko **Nové úlohy**.
2. Pojmenujte úlohu **MyJob**, klepněte na tlačítko **Vytvořit úlohu softwaru ve volném stylu**a potom klepněte na tlačítko **OK**.
3. V části **Sestavení** konfigurace úlohy klepněte na **tlačítko Přidat krok sestavení** a zvolte Spustit **dávkový příkaz systému Windows**.
4. V **příkazu Command**použijte následující příkazy:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. V části **Akce po sestavení** konfigurace úloh klikněte na **Nahrát artefakty do úložiště objektů blob Microsoft Azure**.
6. V **části Název účtu úložiště**vyberte účet úložiště, který chcete použít.
7. V **poli Název kontejneru**zadejte název kontejneru. (Kontejner bude vytvořen, pokud ještě neexistuje při odeslání artefaktů sestavení.) Můžete použít proměnné prostředí, takže v tomto příkladu zadejte **${JOB_NAME}** jako název kontejneru.
   
    **Tip**
   
    Pod oddílem **Příkaz,** kde jste zadali skript pro **příkaz Spustit systém Windows,** je odkaz na proměnné prostředí rozpoznané aplikací Hudson. Kliknutím na tento odkaz se dozvíte názvy a popisy proměnných prostředí. Proměnné prostředí, které obsahují speciální znaky, jako je například proměnná **prostředí BUILD_URL,** nejsou povoleny jako název kontejneru nebo společné virtuální cesty.
8. V tomto příkladu klikněte na nastavit **nový kontejner jako veřejný.** (Pokud chcete použít soukromý kontejner, budete muset vytvořit sdílený přístupový podpis, který umožní přístup. To je nad rámec tohoto článku. Další informace o sdílených přístupových podpisech najdete v [části Použití sdílených přístupových podpisů (SAS).](storage-sas-overview.md)
9. [Nepovinné] Klepněte na **tlačítko Vyčistit kontejner před nahráním,** pokud chcete, aby kontejner vymazány obsahu před artefakty sestavení jsou odeslány (ponechat nezaškrtnuté, pokud nechcete vyčistit obsah kontejneru).
10. **Chcete-li nahrát seznam artefaktů**, zadejte **text/*.txt**.
11. Pro **společnou virtuální cestu pro nahrané artefakty**zadejte **\_${BUILD\_ID}/${BUILD NUMBER}**.
12. Kliknutím na **Uložit** nastavení uložte.
13. Chcete-li spustit **funkci MyJob**, klepněte na řídicípanel Hudson na **tlačítko Sestavit** . Zkontrolujte, zda výstup konzoly není k onomu stavu. Stavové zprávy pro Azure Storage budou zahrnuty do výstupu konzoly, když akce po sestavení začne nahrávat artefakty sestavení.
14. Po úspěšném dokončení úlohy můžete prozkoumat artefakty sestavení otevřením veřejného objektu blob.
    
    a. Přihlaste se k [portálu Azure](https://portal.azure.com).
    
    b. Klepněte na **položku Úložiště**.
    
    c. Klikněte na název účtu úložiště, který jste použili pro Hudson.
    
    d. Klikněte na **Kontejnery**.
    
    e. Klikněte na kontejner s názvem **moje úloha**, což je malá verze názvu úlohy, kterou jste přiřadili při vytváření úlohy Hudson. Názvy kontejnerů a názvy objektů blob jsou malá písmena (a malá a velká písmena) ve službě Azure Storage. V seznamu objektů BLOB pro kontejner s názvem **myjob** byste měli vidět **hello.txt** a **date.txt**. Zkopírujte adresu URL jedné z těchto položek a otevřete ji v prohlížeči. Zobrazí se textový soubor, který byl nahrán jako artefakt sestavení.

Na jednu úlohu se dá vytvořit jenom jedna akce po sestavení, která nahraje artefakty do úložiště objektů blob Azure. Jediná akce po sestavení pro nahrání artefaktů do úložiště objektů Blob Azure můžete určit různé soubory (včetně zástupných znaků) a cesty k souborům v rámci **seznamu artefaktů k nahrání** pomocí středníku jako oddělovač. Pokud například vaše sestavení Hudson vytvoří soubory JAR a TXT ve složce **sestavení** pracovního prostoru a chcete nahrát do úložiště objektů Blob Azure, použijte následující **hodnotu seznamu artefaktů k nahrání** hodnoty: **build/\*.jar;build/\*.txt**. Syntaxe dvojitédvojdvojdvojici můžete také určit cestu, která se má použít v názvu objektu blob. Pokud například chcete, aby se jarové soubory nahrály pomocí **binárních souborů** v cestě objektu blob a soubory TXT se nahrály pomocí **oznámení** v cestě objektu blob, použijte následující hodnotu pro **nahrajte hodnotu Seznamu artefaktů:** **\*build/ .jar::binární soubory;build/\*.txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Jak vytvořit krok sestavení, který se stáhne z úložiště objektů Blob Azure
Následující kroky ukazují, jak nakonfigurovat krok sestavení ke stažení položek z úložiště objektů Blob Azure. To by bylo užitečné, pokud chcete zahrnout položky ve vašem sestavení, například JARs, které uchováte v úložišti objektů blob Azure.

1. V části **Sestavení** konfigurace úlohklikněte na **Přidat krok sestavení** a zvolte Stáhnout z úložiště objektů **Blob Azure**.
2. V **části Název účtu úložiště**vyberte účet úložiště, který chcete použít.
3. V **poli Název kontejneru**zadejte název kontejneru, který má objekty BLOB, které chcete stáhnout. Můžete použít proměnné prostředí.
4. V **poli Název objektu blob**zadejte název objektu blob. Můžete použít proměnné prostředí. Můžete také použít hvězdičku jako zástupný znak po zadání počáteční písmeno název objektu blob. **Například projekt\\*** by určit všechny objekty BLOB, jejichž názvy začínají **projektu**.
5. [Nepovinné] Pro **cestu ke stažení**zadejte cestu na hudsonském počítači, do kterého chcete stahovat soubory z úložiště objektů Blob Azure. Lze také použít proměnné prostředí. (Pokud nezadáte hodnotu pro **cestu ke stažení**, soubory z úložiště objektů Blob Azure se stáhnou do pracovního prostoru úlohy.)

Pokud máte další položky, které chcete stáhnout z úložiště objektů Blob Azure, můžete vytvořit další kroky sestavení.

Po spuštění sestavení můžete zkontrolovat výstup konzoly historie sestavení nebo se podívat na umístění stahování a zjistit, zda byly objekty BLOB, které jste očekávali, úspěšně staženy.

## <a name="components-used-by-the-blob-service"></a>Součásti používané službou Blob
Následující informace obsahují přehled součástí služby Objektů blob.

* **Účet úložiště**: Veškerý přístup k Azure Storage se provádí prostřednictvím účtu úložiště. Toto je nejvyšší úroveň oboru názvů pro přístup k objektům BLOB. Účet může obsahovat neomezený počet kontejnerů, pokud je jejich celková velikost menší než 100 TB.
* **Kontejner**: Kontejner poskytuje seskupení sady objektů BLOB. Všechny objekty blob musí být v kontejneru. Účet může obsahovat neomezený počet kontejnerů. Kontejner můžete pojmout neomezený počet objektů blob.
* **Blob**: Soubor libovolného typu a velikosti. Existují dva typy objektů BLOB, které se můžou ukládat ve službě Azure Storage: objekty BLOB bloku a stránky. Většina souborů jsou objekty BLOB bloku. Jeden blok blob může mít velikost až 200 GB. Tento kurz používá objekty BLOB bloku. Objekty BLOB stránky, jiný typ objektu blob, může mít velikost až 1 TB a jsou efektivnější při časté úpravy rozsahů bajtů v souboru. Další informace o objektech BLOB najdete [v tématu Principy objektů BLOB bloků, objektů BLOB pro připojení a objektů BLOB stránky](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Formát adresy URL**: Objekty BLOB lze adresovat pomocí následujícího formátu adresy URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Výše uvedený formát platí pro globální cloud Azure. Pokud používáte jiný cloud Azure, použijte koncový bod na [webu Azure Portal](https://portal.azure.com) k určení koncového bodu adresy URL.)
  
    Ve výše uvedeném formátu `storageaccount` představuje název `container_name` účtu úložiště, představuje název `blob_name` kontejneru a představuje název objektu blob, resp. V rámci názvu kontejneru můžete mít více cest oddělených lomítkem **/**. Příklad názvu kontejneru v tomto kurzu byla **MyJob**a **${BUILD\_ID}/${BUILD\_NUMBER}** byla použita pro společnou virtuální cestu, což vedlo k tomu, že objekt blob měl adresu URL následujícího formuláře:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Další kroky
* [Seznámit se s Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Sada SDK služby Azure Storage pro Javu](https://github.com/azure/azure-storage-java)
* [Referenční informace ke klientské sadě SDK služby Azure Storage](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog týmu Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/)

Další informace najdete na webu [Azure pro vývojáře v Javě](/java/azure).
