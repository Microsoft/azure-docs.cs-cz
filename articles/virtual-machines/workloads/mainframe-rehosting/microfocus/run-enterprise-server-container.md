---
title: Spusťte Server Enterprise Micro fokus 4.0 v kontejneru Dockeru v Azure Virtual Machines
description: Změna hostitele úloh sálové počítače IBM z/OS spuštěním Micro fokus Enterprise Server v kontejneru Dockeru v Azure Virtual Machines.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896323"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Spuštění Micro fokus Enterprise Server 4.0 v kontejneru Dockeru v Azure

Micro fokus Enterprise Server 4.0 můžete spustit v kontejneru Dockeru v Azure. V tomto kurzu se dozvíte, jak. Enterprise Server používá ukázku acctdemo CICS Windows (systém řízení informací o zákaznících).

Docker přidá do aplikací přenositelnost a izolaci. Například můžete exportovat image Dockeru z jednoho virtuálního počítače Windows pro spuštění na jiné, nebo z úložiště na Windows server s Dockerem. Image Dockeru běží v novém umístění se stejnou konfigurací – bez nutnosti instalace serveru pro organizace. Její součástí bitové kopie. Důležité informace o licencování stále platí.

V tomto kurzu se nainstaluje **Windows 2016 Datacenter s kontejnery** virtuálních počítačů (VM) na Azure Marketplace. Tento virtuální počítač obsahuje **Docker 18.09.0**. Následující kroky ukazují, jak nasazení kontejneru, spusťte ho a pak k němu připojit pomocí 3270 emulátoru.

## <a name="prerequisites"></a>Požadavky

Než začnete, projděte si tyto požadavky:

- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Software Micro fokus a platnou licenci (nebo licenci na zkušební verzi). Pokud jste stávající zákazník Micro fokus, obraťte se na zástupce Micro fokus. V opačném případě [požádat o zkušební verzi](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

     > [!NOTE]
     > Ukázkové soubory Docker jsou součástí Enterprise Server 4.0. Tento kurz používá ent\_server\_soubory dockerfile\_4.0\_windows.zip. Přístup ze na stejném místě získat přístup k souboru instalace Enterprise serveru, nebo přejít na *Micro fokus* začít.

- V dokumentaci k [Enterprise Server a podnikový vývojář](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Média zabezpečit z ent\_server\_soubory dockerfile\_4.0\_windows.zip souboru. Zabezpečení licencování souboru ES-Docker-Prod-XXXXXXXX.mflic (požadováno pro vytváření imagí Dockeru).

2. Vytvoření virtuálního počítače. Chcete-li to provést, otevřete Azure portal, vyberte **vytvořit prostředek** z levého horního rohu a filtrovat podle *systému windows server*. Ve výsledcích vyberte **systému Windows Server 2016 Datacenter – s kontejnery**.

     ![Výsledky vyhledávání v portálu Azure](media/01-portal.png)

3. Chcete-li konfigurovat vlastnosti pro virtuální počítač, zvolte podrobnosti o instanci:

    1. Vyberte velikost virtuálního počítače. Pro účely tohoto kurzu, zvažte použití **Standard DS2\_v2** virtuální počítač s 2 virtuálních procesorů a 7 GB paměti.

    2. Vyberte **oblasti** a **skupiny prostředků** chcete nasadit.

    3. Pro **možností dostupnosti**, použijte výchozí nastavení.

    4. Pro **uživatelské jméno**, zadejte účet správce, který chcete použít a heslo.

    5. Ujistěte se, že **port 3389 RDP** je otevřený. Jenom Tenhle port musíte být veřejně zpřístupnit, abyste mohli přihlásit k virtuálnímu počítači. Potom přijměte všechny výchozí hodnoty a klikněte na tlačítko **revize + vytvořit**.

     ![Vytvořit virtuální počítač podokno](media/container-02.png)

4. Počkejte na dokončení (několik minut) nasazení. Zpráva hlásí, že váš virtuální počítač vytvořil.

5. Klikněte na tlačítko **přejít k prostředku** přejdete **přehled** okně pro virtuální počítač.

6. Na pravé straně, klikněte na tlačítko **připojit** tlačítko. **Připojit k virtuálnímu počítači** možnosti se zobrazí na pravé straně.

7. Klikněte na tlačítko **stáhnout soubor RDP** tlačítko a stáhněte si soubor RDP, který umožňuje připojení k virtuálnímu počítači.

8. Po dokončení stahování souboru otevřete ho a zadejte uživatelské jméno a heslo, které jste vytvořili pro virtuální počítač.

     > [!NOTE]
     > Nepoužívejte své podnikové přihlašovací údaje pro přihlášení. (Klienta protokolu RDP předpokládá, že chcete použít. Ho použít nechcete.)

9.  Vyberte **víc možností**, pak vyberte své přihlašovací údaje virtuálního počítače.

V tomto okamžiku je virtuální počítač spuštěný a připojený přes protokol RDP. Jste zaregistrovaní a být připravené na další krok.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Vytvořte adresář izolovaného prostoru a nahrání souboru zip

1.  Vytvořte adresář na virtuálním počítači, kde můžete nahrát soubory ukázky a licence. Například **C:\\izolovaného prostoru**.

2.  Nahrát **ent\_server\_soubory dockerfile\_4.0\_windows.zip** a **ES-Docker-Prod-XXXXXXXX.mflic** soubor do adresáře, který jste vytvořili.

3.  Extrahujte obsah souboru zip, který má **ent\_server\_soubory dockerfile\_4.0\_windows** adresář, vytvoří pomocí procesu extrakce. Tento adresář obsahuje soubor readme (jako soubor HTML a txt) a dvě podsložky **EnterpriseServer** a **příklady**.

4.  Kopírování **ES-Docker-Prod-XXXXXXXX.mflic** na jednotce C:\\izolovaného prostoru\\ent\_server\_soubory dockerfile\_4.0\_windows\\ EnterpriseServer a C:\\izolovaného prostoru\\ent\_server\_soubory dockerfile\_4.0\_windows\\příklady\\CICS adresáře.

> [!NOTE]
> Ujistěte se, že zkopírujete licenční soubor do obou adresářů. Jsou požadována pro krok sestavení Dockeru zajistit, aby že byly řádně licencované Image.

## <a name="check-docker-version-and-create-base-image"></a>Zkontrolujte verzi Dockeru a vytvořte základní image

> [!IMPORTANT]
> Vytvoření odpovídající image Dockeru je dvoustupňový proces. Nejprve vytvořte základní image Enterprise Server 4.0. Pak vytvořte jinou bitovou kopii pro x64 platformy. I když můžete vytvořit x86 (32 bitů) bitovou kopii, použijte 64bitovou kopii.

1. Otevřete příkazový řádek.

2. Zkontrolujte, jestli je Docker nainstalovaný. Na příkazovém řádku zadejte:

    ```
        docker version
    ```

     Například verze byla 18.09.0, pokud to byla zapsána.

3. Chcete-li změnit adresář, zadejte **cd \Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer**.

4. Typ **bld.bat IacceptEULA** zahájíte proces sestavení pro počáteční základní image. Počkejte několik minut, než se tento proces běžel. Ve výsledcích, Všimněte si, že dvě bitové kopie, které byly vytvořeny – jeden pro x64 a jeden pro x86:

     ![Příkazové okno zobrazení obrázků](media/container-04.png)

5. Chcete-li vytvořit finální image CICS ukázku, přejděte do adresáře CICS zadáním **cd\\izolovaného prostoru\\ent\_server\_soubory dockerfile\_4.0\_windows\\ Příklady\\CICS**.

6. Chcete-li vytvořit bitovou kopii, zadejte **bld.bat x64**. Počkejte několik minut, než se proces pro spuštění a zpráva oznamující, že image byla vytvořena.

7. Typ **imagí dockeru** zobrazíte seznam všech imagí Dockeru, který je nainstalovaný na virtuálním počítači. Ujistěte se, že **microfocus/es-acctdemo** je jedním z nich.

     ![Příkazové okno zobrazující es acctdemo image](media/container-05.png)

## <a name="run-the-image"></a>Spusťte image 

1.  Spusťte Enterprise Server 4.0 a acctdemo aplikace, zadejte na příkazovém řádku:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Nainstalovat 3270 emulátoru terminálu, jako je [x3270](http://x3270.bgp.nu/) a použít ho k připojení přes port 9040, obrázek, který je spuštěn.

3.  Získejte IP adresu kontejneru acctdemo, takže Docker může fungovat jako server DHCP pro kontejnery spravuje:

    1.  Získejte ID spuštěného kontejneru. Typ **Docker ps** na příkazovém řádku a Všimněte si ID (**22a0fe3159d0** v tomto příkladu). Uložte ho na další krok.

    2.  Chcete-li získat IP adresu pro kontejner acctdemo, použijte ID kontejneru z předchozího kroku následujícím způsobem:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Příklad:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Poznamenejte si IP adresu pro acctdemo image. Například adresa v následujícím výstupu je 172.19.202.52.

     ![Příkazové okno zobrazuje IP adresa](media/container-06.png)

5. Připojte image pomocí emulátoru. Emulátor pro použití adresy acctdemo image a port 9040 konfigurace. Tady má **172.19.202.52:9040**. Bude podobná. **Sign-On pro CICS** otevře se obrazovka.

    ![Sign-on CICS obrazovku](media/container-07.png)

6. Přihlaste se k oblasti CICS tak, že zadáte **SYSAD** pro **USERID** a **SYSAD** pro **heslo**.

7. Vymažou obrazovku, pomocí keymap v emulátoru. X3270, vyberte **Keymap** nabídky.

8. Chcete-li spustit aplikaci acctdemo, zadejte **ACCT**. Zobrazí se úvodní obrazovka aplikace.

     ![Ukázka obrazovky účtu](media/container-08.png)

9. Můžete experimentovat s typy účtů zobrazení. Zadejte například **D** pro požadavek a **11111** pro **účet**. Jiné účty zkuste jsou 22222 33333 a tak dále.

     ![Ukázka obrazovky účtu](media/container-09.png)

10. Chcete-li zobrazit konzole pro správu serveru Enterprise, přejděte na příkazový řádek a zadejte **start http:172.19.202.52:86**

     ![Konzola pro správu serveru Enterprise](media/container-010.png)

A to je vše! Nyní máte spouštění a správu CICS aplikace v kontejneru Dockeru.

## <a name="next-steps"></a>Další postup

- [Instalace Micro fokus Enterprise Server 4.0 a podnikový vývojář 4.0 v Azure](./set-up-micro-focus-azure.md)
- [Migrace mainframových aplikací](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
