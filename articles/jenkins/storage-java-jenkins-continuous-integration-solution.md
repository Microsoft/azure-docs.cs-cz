---
title: Používání Azure Storage s řešením jenkinsové průběžné integrace
description: Tento kurz ukazuje, jak používat službu objektů blob Azure jako úložiště pro artefakty sestavení vytvořené řešením nepřetržité integrace Jenkinse.
keywords: jenkins, azurové, devops, skladování, cicd
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: df1d59c40fd09fb055db9d7622d86ff9c82991b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624680"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Používání Azure Storage s řešením jenkinsové průběžné integrace

Tento článek ukazuje, jak používat úložiště objektů Blob jako úložiště artefaktů sestavení vytvořených řešením jenkinsové průběžné integrace (CI) nebo jako zdroj souborů ke stažení, které mají být použity v procesu sestavení. Jedním ze scénářů, kde byste toto řešení považovali za užitečné, je, když kódujete v agilním vývojovém prostředí (pomocí jazyka Java nebo jiných jazyků), sestavení běží na základě průběžné integrace a potřebujete úložiště pro artefakty sestavení, takže můžete je například sdílet s ostatními členy organizace, se zákazníky nebo udržovat archiv. Jiný scénář je, když vaše úloha sestavení sama o sobě vyžaduje další soubory, například závislosti ke stažení jako součást vstupu sestavení.

V tomto kurzu budete používat modul plug-in úložiště Azure pro Jenkins E k dispozici společností Microsoft.

## <a name="jenkins-overview"></a>Jenkins – přehled

Jenkins umožňuje průběžnou integraci softwarového projektu tím, že umožňuje vývojářům snadno integrovat své změny kódu a mít sestavení vyrobené automaticky a často, čímž se zvyšuje produktivita vývojářů. Sestavení jsou verzí a artefakty sestavení lze nahrát do různých úložišť. Tento článek ukazuje, jak používat úložiště objektů blob Azure jako úložiště artefaktů sestavení. Také ukáže, jak stahovat závislosti z úložiště objektů blob Azure.

Více informací o Jenkinsi naleznete na adrese [Meet Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Výhody použití služby Blob

Mezi výhody použití služby Blob k hostování artefaktů agilního vývojového sestavení patří:

* Vysoká dostupnost artefaktů sestavení a nebo závislostí ke stažení.
* Výkon při nahrávání artefaktů sestavení jenkinsem CI.
* Výkon, když vaši zákazníci a partneři stáhnout artefakty sestavení.
* Kontrola nad zásadami přístupu uživatelů s možností volby mezi anonymním přístupem, přístupem k sdílenému přístupu založeným na vypršení platnosti, soukromým přístupem atd.

## <a name="prerequisites"></a>Požadavky

* Jenkinsovo řešení pro průběžnou integraci.
  
    Pokud aktuálně nemáte řešení Jenkins CI, můžete spustit řešení Jenkins CI pomocí následující techniky:
  
  1. Na počítači s podporou javy <https://jenkins-ci.org>stáhněte soubor jenkins.war z .
  2. Na příkazovém řádku, který je otevřen do složky, která obsahuje soubor jenkins.war, spusťte:
     
      `java -jar jenkins.war`

  3. Otevřete v `http://localhost:8080/` prohlížeči panel Jenkinse, který použijete k instalaci a konfiguraci pluginu Azure Storage.
     
      Zatímco typické řešení Jenkins CI by být nastavena tak, aby spustit jako služba, spuštění Jenkins války na příkazovém řádku bude stačit pro tento kurz.
* Účet Azure. Účet Azure si můžete zaregistrovat na adrese <https://www.azure.com>.
* Účet úložiště Azure. Pokud ještě nemáte účet úložiště, můžete si ho vytvořit pomocí kroků na [jdete vytvořit účet úložiště](../storage/common/storage-account-create.md).
* Znalost řešení Jenkins CI se doporučuje, ale není vyžadováno, protože následující obsah použije základní příklad, který vám ukáže kroky potřebné při použití služby Blob jako úložiště artefaktů sestavení Jenkins CI.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Použití služby Blob s Jenkinsem CI
Pokud chcete službu Blob používat s Jenkinsem, budete muset nainstalovat plugin Azure Storage, nakonfigurovat plugin tak, aby používal váš účet úložiště, a pak vytvořit akci po sestavení, která nahraje artefakty sestavení do vašeho účtu úložiště. Tyto kroky jsou popsány v následujících částech.

## <a name="how-to-install-the-azure-storage-plugin"></a>Jak nainstalovat plugin azure storage
1. Na řídicím panelu Jenkinsvyberte **Spravovat Jenkinse**.
2. Na stránce **Spravovat Jenkinsvyberte** **Spravovat moduly plug-in**.
3. Vyberte kartu **Available** (K dispozici).
4. V části **Zasazovači artefaktů** zkontrolujte **plugin úložiště Microsoft Azure**.
5. Vyberte možnost **Nainstalovat bez restartování** nebo Stáhnout a nainstalovat po **restartování**.
6. Restartujte Jenkinse.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Jak nakonfigurovat plugin Azure Storage tak, aby používal váš účet úložiště
1. Na řídicím panelu Jenkinsvyberte **Spravovat Jenkinse**.
2. Na stránce **Spravovat Jenkinsvyberte** **Konfigurovat systém**.
3. V části **Konfigurace účtu úložiště Microsoft Azure:**
   1. Zadejte název účtu úložiště, který můžete získat z [webu Azure Portal](https://portal.azure.com).
   2. Zadejte klíč účtu úložiště, který lze získat také z [portálu Azure](https://portal.azure.com).
   3. Pokud používáte globální cloud Azure, použijte výchozí hodnotu **pro adresu URL koncového bodu služby Blob** Service. Pokud používáte jiný cloud Azure, použijte koncový bod, jak je uvedeno na [portálu Azure](https://portal.azure.com) pro váš účet úložiště. 
   4. Vyberte **Ověřit přihlašovací údaje úložiště,** chcete-li ověřit účet úložiště. 
   5. [Nepovinné] Pokud máte další účty úložiště, které chcete zpřístupnit svému jenkinsovému ci, vyberte **Přidat další účty úložiště**.
   6. Chcete-li uložit nastavení, vyberte **Uložit.**

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Jak vytvořit akci po sestavení, která nahraje artefakty sestavení do vašeho účtu úložiště
Pro instruktážní účely musíte nejprve vytvořit úlohu, která vytvoří několik souborů, a pak přidat do akce po sestavení, abyste je nahráli do účtu úložiště.

1. Na řídicím panelu Jenkins vyberte **Nová položka**.
2. Pojmenujte úlohu **MyJob**, vyberte **Vytvořit softwarový projekt volného stylu**a pak vyberte **OK**.
3. V části **Sestavení** konfigurace úlohy vyberte **Přidat krok sestavení** a vyberte Spustit **dávkový příkaz Systému Windows**.
4. V **příkazu Command**použijte následující příkazy:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. V části **Akce po sestavení** konfigurace úlohvyberte Přidat akci po **sestavení** a vyberte **Nahrát artefakty do úložiště objektů Blob Azure**.
6. V **části Název účtu úložiště**vyberte účet úložiště, který chcete použít.
7. Do **pole Název kontejneru**zadejte název kontejneru. (Kontejner bude vytvořen, pokud ještě neexistuje při odeslání artefaktů sestavení.) Můžete použít proměnné prostředí, takže v `${JOB_NAME}` tomto příkladu zadejte jako název kontejneru.
   
    **Tip**
   
    Pod oddílem **Příkaz,** kde jste zadali skript pro **dávkový příkaz Spustit systém Windows,** je odkaz na proměnné prostředí rozpoznané jenkinsem. Vyberte tento odkaz, abyste se dozvěděli názvy a popisy proměnných prostředí. Proměnné prostředí, které obsahují speciální znaky, jako je například proměnná **prostředí BUILD_URL,** nejsou povoleny jako název kontejneru nebo společné virtuální cesty.
8. V yberte **Vytvořit nový kontejner ve výchozím nastavení** pro tento příklad. (Pokud chcete použít soukromý kontejner, budete muset vytvořit sdílený přístupový podpis, který umožní přístup, což je nad rámec tohoto článku. Další informace o sdílených přístupových podpisech najdete v [části Použití sdílených přístupových podpisů (SAS).](../storage/common/storage-sas-overview.md)
9. [Nepovinné] Vyberte **vyčistit kontejner před nahráním,** pokud chcete, aby kontejner být vymazány obsahu před artefakty sestavení jsou odeslány (ponechat nezaškrtnuté, pokud nechcete vyčistit obsah kontejneru).
10. **Chcete-li nahrát seznam artefaktů**, zadejte `text/*.txt`.
11. Pro **společnou virtuální cestu pro nahrané artefakty**zadejte `${BUILD\_ID}/${BUILD\_NUMBER}`pro účely tohoto kurzu .
12. Chcete-li uložit nastavení, vyberte **Uložit.**
13. Na řídicím panelu Jenkinse vyberte **Sestavit nyní** a spusťte **myjob**. Zkontrolujte, zda výstup konzoly není k onomu stavu. Stavové zprávy pro úložiště Azure budou zahrnuty do výstupu konzoly, když akce po sestavení začne nahrávat artefakty sestavení.
14. Po úspěšném dokončení úlohy můžete prozkoumat artefakty sestavení otevřením veřejného objektu blob.
    1. Přihlaste se k [portálu Azure](https://portal.azure.com).
    2. Vyberte **možnost Úložiště**.
    3. Vyberte název účtu úložiště, který jste použili pro Jenkinse.
    4. Vyberte **kontejnery**.
    5. Vyberte kontejner s názvem **moje úloha**, což je malá verze názvu úlohy, kterou jste přiřadili při vytváření úlohy Jenkinse. Názvy kontejnerů a názvy objektů blob jsou malá písmena (a malá a velká písmena) v úložišti Azure. V seznamu objektů BLOB pro kontejner s názvem **myjob**, měli byste vidět **hello.txt** a **date.txt**. Zkopírujte adresu URL jedné z těchto položek a otevřete ji v prohlížeči. Zobrazí se textový soubor, který byl nahrán jako artefakt sestavení.

Na jednu úlohu se dá vytvořit jenom jedna akce po sestavení, která nahraje artefakty do úložiště objektů blob Azure. Jediná akce po sestavení k nahrání artefaktů do úložiště objektů blob Azure můžete určit různé soubory (včetně zástupných znaků) a cesty k souborům v rámci **Seznamu artefaktů k nahrání** pomocí středníku jako oddělovač. Pokud například vaše sestavení Jenkinse vytvoří soubory JAR a TXT ve složce **sestavení** pracovního prostoru a chcete nahrát do úložiště objektů blob `build/\*.jar;build/\*.txt`Azure, použijte následující hodnotu pro možnost **Seznam artefaktů k nahrání:** . Syntaxe dvojitédvojdvojdvojici můžete také určit cestu, která se má použít v názvu objektu blob. Pokud například chcete, aby se jarové záznamy nahrály pomocí **binárních souborů** v cestě k blob a soubory TXT se nahrály pomocí `build/\*.jar::binaries;build/\*.txt::notices` **oznámení** v cestě objektu blob, použijte následující hodnotu pro možnost **Nahrát seznam artefaktů:** .

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Jak vytvořit krok sestavení, který se stáhne z úložiště objektů blob Azure
Následující kroky ilustrují konfiguraci kroku sestavení pro stažení položek z úložiště objektů blob Azure, což je užitečné, pokud chcete zahrnout položky do sestavení. Příkladem použití tohoto vzoru je JARs, které můžete chtít zachovat v úložišti objektů blob Azure.

1. V části **Sestavení** konfigurace úlohy vyberte **Přidat krok sestavení** a vyberte Stáhnout z úložiště objektů **Blob Azure**.
2. V **části Název účtu úložiště**vyberte účet úložiště, který chcete použít.
3. V **poli Název kontejneru**zadejte název kontejneru, který má objekty BLOB, které chcete stáhnout. Můžete použít proměnné prostředí.
4. V **poli Název objektu blob**zadejte název objektu blob. Můžete použít proměnné prostředí. Můžete také použít hvězdičku jako zástupný znak po zadání počáteční písmeno název objektu blob. **Například projekt\\*** by určit všechny objekty BLOB, jejichž názvy začínají **projektu**.
5. [Nepovinné] Pro **cestu ke stažení**zadejte cestu na jenkinsském počítači, do kterého chcete stahovat soubory z úložiště objektů blob Azure. Lze také použít proměnné prostředí. (Pokud nezadáte hodnotu pro **cestu ke stažení**, soubory z úložiště objektů blob Azure se stáhnou do pracovního prostoru úlohy.)

Pokud máte další položky, které chcete stáhnout z úložiště objektů blob Azure, můžete vytvořit další kroky sestavení.

Po spuštění sestavení můžete zkontrolovat výstup konzoly historie sestavení nebo se podívat na umístění stahování a zjistit, zda byly objekty BLOB, které jste očekávali, úspěšně staženy.  

## <a name="components-used-by-the-blob-service"></a>Součásti používané službou Blob
Tato část obsahuje přehled součástí služby objektů blob.

* **Účet úložiště**: Veškerý přístup k Azure Storage se provádí prostřednictvím účtu úložiště. Účet úložiště je nejvyšší úroveň oboru názvů pro přístup k objektům BLOB. Účet může obsahovat neomezený počet kontejnerů, pokud je jejich celková velikost menší než 100 TB.
* **Kontejner**: Kontejner poskytuje seskupení sady objektů BLOB. Všechny objekty blob musí být v kontejneru. Účet může obsahovat neomezený počet kontejnerů. Kontejner můžete pojmout neomezený počet objektů blob.
* **Blob**: Soubor libovolného typu a velikosti. Existují dva typy objektů BLOB, které se můžou ukládat ve službě Azure Storage: objekty BLOB bloku a stránky. Většina souborů jsou objekty BLOB bloku. Jeden blok blob může mít velikost až 200 GB. Tento kurz používá objekty BLOB bloku. Objekty BLOB stránky, jiný typ objektu blob, může mít velikost až 1 TB a jsou efektivnější při časté úpravy rozsahů bajtů v souboru. Další informace o objektech BLOB najdete [v tématu Principy objektů BLOB bloků, objektů BLOB pro připojení a objektů BLOB stránky](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Formát adresy URL**: Objekty BLOB lze adresovat pomocí následujícího formátu adresy URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Výše uvedený formát platí pro globální cloud Azure. Pokud používáte jiný cloud Azure, použijte koncový bod na [webu Azure Portal](https://portal.azure.com) k určení koncového bodu adresy URL.)
  
    Ve výše uvedeném formátu `storageaccount` představuje název `container_name` účtu úložiště, představuje název `blob_name` kontejneru a představuje název objektu blob, resp. V rámci názvu kontejneru můžete mít více cest oddělených lomítkem **/**. Příklad názvu kontejneru použitého pro tento kurz byl **MyJob**a **${BUILD\_ID}/${BUILD\_NUMBER}** byl použit pro společnou virtuální cestu, což vedlo k tomu, že objekt blob měl adresu URL následujícího formuláře:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Řešení potíží s modulem plug-in Jenkinse

Pokud v modulech plug-in Jenkinse narazíte na nějaké chyby, založte problém na stránce [Jenkins JIRA](https://issues.jenkins-ci.org/) pro konkrétní komponentu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Jenkins v Azure](/azure/Jenkins/)