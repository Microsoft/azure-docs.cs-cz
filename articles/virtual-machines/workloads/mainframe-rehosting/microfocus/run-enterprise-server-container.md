---
title: Provozování Micro Enterprise serveru 4,0 v kontejneru Docker v Azure Virtual Machines
description: Přehostování úloh sálového počítače IBM z/OS spuštěním podnikového serveru Micro na kontejneru Docker v Azure Virtual Machines.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2020
ms.locfileid: "61488340"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Provozování Micro Enterprise serveru 4,0 v kontejneru Docker v Azure

Micro Micro Enterprise Server 4,0 můžete spustit v kontejneru Docker v Azure. V tomto kurzu se dozvíte, jak. Používá Windows CICS (Customer Information Control System) acctdemo pro podnikový server.

Docker přidá do aplikací přenositelnost a izolaci. Můžete například exportovat Docker image z jednoho virtuálního počítače s Windows pro spuštění na jiném nebo z úložiště na Windows Server s Docker. Image Docker se spouští v novém umístění se stejnou konfigurací, aniž by bylo nutné instalovat podnikový server. Je součástí obrázku. Licenční požadavky se pořád týkají.

Tento kurz nainstaluje **Windows 2016 Datacenter s kontejnery** virtuální počítač (VM) z Azure Marketplace. Tento virtuální počítač obsahuje **Docker 18.09.0**. Následující postup ukazuje, jak nasadit kontejner, spustit ho a pak se k němu připojit pomocí emulátoru 3270.

## <a name="prerequisites"></a>Požadavky

Než začnete, podívejte se na tyto požadavky:

- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Software zaměřený na Micro a platnou licenci (nebo zkušební licenci). Pokud jste stávajícím zákazníkem s fokusem, obraťte se na svého zástupce Micro Focus. V opačném případě [požádejte o zkušební verzi](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

     > [!NOTE]
     > Ukázkové soubory Docker jsou součástí podnikového serveru 4,0. V tomto kurzu se\_používá\_4,0\_\_fázemi Windows. zip pro \ Server \. Můžete k němu přistupovat ze stejného místa, ke kterému jste získali přístup k instalačnímu souboru podnikového serveru, nebo můžete přejít na *Micro Focus* a začít.

- Dokumentace k [podnikovým serverům a podnikovým vývojářům](https://www.microfocus.com/documentation/enterprise-developer/#")

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Zabezpečte médium ze souboru s\_fázemi\_\_4,0\_Windows. zip serveru. Zabezpečte soubor licencí ES-Docker-prod-XXXXXXXX. mflic (potřebný k sestavení imagí Docker).

2. Vytvořte virtuální počítač. Provedete to tak, že otevřete Azure Portal, vyberete **vytvořit prostředek** v levém horním rohu a vyfiltrujete ho podle *Windows serveru*. Ve výsledcích vyberte **Windows Server 2016 Datacenter – s kontejnery**.

     ![Azure Portal výsledků hledání](media/01-portal.png)

3. Pokud chcete nakonfigurovat vlastnosti pro virtuální počítač, vyberte podrobnosti instance:

    1. Vyberte velikost virtuálního počítače. Pro účely tohoto kurzu zvažte použití standardního virtuálního počítače s **DS2\_v2** a 2 vCPU a 7 GB paměti.

    2. Vyberte **oblast** a **skupinu prostředků** , do které chcete nasadit.

    3. Pro **Možnosti dostupnosti**použijte výchozí nastavení.

    4. Jako **uživatelské jméno**zadejte účet správce, který chcete použít, a heslo.

    5. Ujistěte se, že je **port 3389 RDP** otevřený. Pouze tento port musí být veřejně vystavený, takže se můžete přihlásit k virtuálnímu počítači. Pak přijměte všechny výchozí hodnoty a klikněte na tlačítko **zkontrolovat + vytvořit**.

     ![Podokno vytvořit virtuální počítač](media/container-02.png)

4. Počkejte na dokončení nasazení (několik minut). Zobrazí se zpráva, že byl virtuální počítač vytvořen.

5. Kliknutím na **Přejít k prostředku** přejdete do okna **Přehled** pro váš virtuální počítač.

6. Napravo klikněte na tlačítko **připojit** . Na pravé straně se zobrazí možnosti **připojit k virtuálnímu počítači** .

7. Klikněte na tlačítko **Stáhnout soubor RDP** a Stáhněte soubor RDP, který vám umožní připojit se k virtuálnímu počítači.

8. Po dokončení stahování souboru ho otevřete a zadejte uživatelské jméno a heslo, které jste vytvořili pro virtuální počítač.

     > [!NOTE]
     > Nepoužívejte své podnikové přihlašovací údaje pro přihlášení. (Klient RDP předpokládá, že je budete chtít použít. Nebudete.)

9.  Vyberte **Další volby**a pak vyberte přihlašovací údaje k virtuálnímu počítači.

V tomto okamžiku je virtuální počítač spuštěný a připojený přes RDP. Jste přihlášení a připraveni k dalšímu kroku.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Vytvoření adresáře izolovaného prostoru (sandbox) a nahrání souboru ZIP

1.  Na virtuálním počítači vytvořte adresář, do kterého můžete nahrát ukázku a licenční soubory. Například **C:\\Sandbox**.

2.  Do adresáře, který jste vytvořili, nahrajte soubor s **\_fázemi\_\_4,0 Windows. zip a soubor ES-Docker-prod-xxxxxxxx. mflic.\_** **ES-Docker-Prod-XXXXXXXX.mflic**

3.  Extrahujte obsah souboru zip do **\_adresáře\_fázemi\_\_4,0 systému Windows** vytvořeného procesem extrakce. Tento adresář obsahuje soubor Readme (jako soubor. html a. txt) a dva podadresáře, **EnterpriseServer** a **Příklady**.

4.  Zkopírujte **ES-Docker-prod-xxxxxxxx. mflic** do\\C: izolovaného\\prostoru\_(\_Sandbox\_)\_fázemi\\4,0 Windows EnterpriseServer a\\c\\:\_izolovaný\_server\_izolovaného\\serveru\_fázemi 4,0 Windows\\příklady CICS adresářů.

> [!NOTE]
> Nezapomeňte zkopírovat soubor licencí do obou adresářů. Jsou vyžadovány pro krok sestavení Docker, aby bylo zajištěno, že jsou bitové kopie správně licencovány.

## <a name="check-docker-version-and-create-base-image"></a>Podívejte se na verzi Docker a vytvořte základní image.

> [!IMPORTANT]
> Vytvoření vhodné image Docker je proces se dvěma kroky. Nejdřív vytvořte základní image Enterprise Server 4,0.Pak vytvořte další image pro platformu x64. I když můžete vytvořit bitovou kopii x86 (32), použijte bitovou kopii 64.

1. Otevřete příkazový řádek.

2. Ověřte, že je Docker nainstalovaný. Na příkazovém řádku zadejte:

    ```
        docker version
    ```

     Například při zápisu verze byla 18.09.0.

3. Chcete-li změnit adresář, zadejte **CD \sandbox\ ent_server_dockerfiles_4.0_windows \enterpriseserver**.

4. Zadejte **Bld. bat IacceptEULA** a zahajte proces sestavení pro počáteční základní bitovou kopii. Počkejte několik minut, než se tento proces spustí. Ve výsledcích si všimněte dvou vytvořených imagí – jeden pro platformu x64 a jeden pro x86:

     ![okno Příkaz zobrazování imagí](media/container-04.png)

5. Pokud chcete vytvořit finální image pro ukázku CICS, přepněte do adresáře CICS zadáním fázemi **\\sandboxu\\\_pro Windows\_\_4,0\_Windows\\Examples\\CICS**.

6. Chcete-li vytvořit bitovou kopii, zadejte **Bld. bat x64**. Počkejte několik minut, než se proces spustí, a zobrazí se zpráva oznamující, že se image vytvořila.

7. Chcete-li zobrazit seznam všech imagí Docker nainstalovaných na virtuálním počítači, zadejte **Docker images** . Zajistěte, aby byl jeden z nich **mikrofocus/ES-acctdemo** .

     ![Okno Příkaz zobrazení obrázku ES-acctdemo](media/container-05.png)

## <a name="run-the-image"></a>Spustit bitovou kopii 

1.  Pokud chcete spustit podnikový server 4,0 a aplikaci acctdemo, zadejte na příkazovém řádku tento příkaz:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Nainstalujte emulátor terminálu 3270, například [x3270](http://x3270.bgp.nu/) , a použijte ho k připojení přes port 9040 k imagi, která je spuštěná.

3.  Získejte IP adresu kontejneru acctdemo, takže Docker může fungovat jako server DHCP pro kontejnery, které spravuje:

    1.  Získá ID běžícího kontejneru. Do příkazového řádku zadejte **Docker PS** a poznamenejte si ID (v tomto příkladu**22a0fe3159d0** ). Uložte si ho pro další krok.

    2.  K získání IP adresy pro kontejner acctdemo použijte ID kontejneru z předchozího kroku následujícím způsobem:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Příklad:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Poznamenejte si IP adresu pro obrázek acctdemo. Například adresa v následujícím výstupu je 172.19.202.52.

     ![okno Příkaz zobrazení IP adresy](media/container-06.png)

5. Připojte Image pomocí emulátoru. Nakonfigurujte emulátor tak, aby používal adresu acctdemo image a port 9040. Tady je **172.19.202.52:9040**. Vaše moje akce bude podobná. Otevře se obrazovka **jednotného to CICS** .

    ![Obrazovka jednotného to CICS](media/container-07.png)

6. Přihlaste se k CICS oblasti tak, že zadáte **SYSAD** pro **UserID** a **SYSAD** pro **heslo**.

7. Vymažte obrazovku pomocí keymap emulátoru. Pro x3270 vyberte možnost nabídky **keymap** .

8. Chcete-li spustit aplikaci acctdemo, zadejte **Acct**. Zobrazí se úvodní obrazovka aplikace.

     ![Ukázková obrazovka účtu](media/container-08.png)

9. Experimentujte s typy účtů zobrazení. Například zadejte **D** pro požadavek a **11111** pro **účet**. Další čísla účtů k vyzkoušení jsou 22222, 33333 a tak dále.

     ![Ukázková obrazovka účtu](media/container-09.png)

10. Pokud chcete zobrazit konzolu pro správu podnikového serveru, otevřete příkazový řádek a zadejte příkaz **Start http: 172.19.202.52:86.**

     ![Konzola pro správu podnikového serveru](media/container-010.png)

A to je vše! Teď spouštíte a spravujete aplikaci CICS v kontejneru Docker.

## <a name="next-steps"></a>Další kroky

- [Instalace Micro Micro Enterprise Server 4,0 a Enterprise Developer 4,0 v Azure](./set-up-micro-focus-azure.md)
- [Migrace mainframových aplikací](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
