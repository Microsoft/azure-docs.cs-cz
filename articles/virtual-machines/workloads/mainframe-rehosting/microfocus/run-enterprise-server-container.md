---
title: Spuštění micro focus enterprise serveru 4.0 v kontejneru Dockeru na virtuálních počítačích Azure
description: Znovu hostujte úlohy sálových počítačů IBM z/OS spuštěním micro focus enterprise serveru v kontejneru Dockeru na virtuálních počítačích Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61488340"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Spuštění micro focus enterprise serveru 4.0 v kontejneru Dockeru v Azure

Micro Focus Enterprise Server 4.0 můžete spustit v kontejneru Dockeru v Azure. Tento výukový program vám ukáže, jak. Používá acctdemo ukázku systému Windows CICS (Customer Information Control System) pro podnikový server.

Docker přidává přenositelnost a izolaci do aplikací. Můžete například exportovat bitovou kopii Dockeru z jednoho virtuálního počítače windows a spustit ji na jiném nebo z úložiště na server Windows s Dockerem. Image Dockeru běží v novém umístění se stejnou konfigurací – bez nutnosti instalace podnikového serveru. Je to součást obrazu. Stále platí aspekty licencování.

Tento kurz nainstaluje **Datové centrum Windows 2016 s kontejnery** virtuálního počítače (VM) z Azure Marketplace. Tento virtuální virtuální virtuální ms zahrnuje **Docker 18.09.0**. Následující kroky ukazují, jak nasadit kontejner, spustit jej a připojit se k němu pomocí emulátoru 3270.

## <a name="prerequisites"></a>Požadavky

Než začnete, podívejte se na tyto požadavky:

- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Software Micro Focus a platná licence (nebo zkušební licence). Pokud jste stávajícím zákazníkem společnosti Micro Focus, obraťte se na svého zástupce společnosti Micro Focus. V opačném případě [požádejte o zkušební verzi](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

     > [!NOTE]
     > Ukázkové soubory Dockeru jsou součástí podnikového serveru 4.0. Tento kurz\_používá\_ent\_server\_dockerfiles 4.0 windows.zip. Získejte k němu přístup ze stejného místa, kde jste získali přístup k instalačnímu souboru podnikového serveru, nebo přejděte na *micro focus* a získejte další možnosti.

- Dokumentace pro [enterprise server a podnikový vývojář](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Zabezpečte médium\_ze\_souboru ent server dockerfiles\_4.0\_windows.zip. Zabezpečte licenční soubor ES-Docker-Prod-XXXXXXXX.mflic (vyžadován k vytvoření bitových kopií Dockeru).

2. Vytvořte virtuální hod. Chcete-li to provést, otevřete portál Azure, vyberte **Vytvořit prostředek** z levého horního rohu a filtrovat podle *windows serveru*. Ve výsledcích vyberte **Windows Server 2016 Datacenter – s kontejnery**.

     ![Výsledky hledání na webu Azure Portal](media/01-portal.png)

3. Chcete-li nakonfigurovat vlastnosti virtuálního počítače, zvolte podrobnosti instance:

    1. Vyberte velikost virtuálního počítače. V tomto kurzu zvažte použití standardního virtuálního uživatele **DS2\_v2** s 2 virtuálními procesory a 7 GB paměti.

    2. Vyberte **oblast** a **skupinu prostředků,** do které chcete nasadit.

    3. V **případě možností dostupnosti**použijte výchozí nastavení.

    4. Do **pole Uživatelské jméno**zadejte účet správce, který chcete použít, a heslo.

    5. Zkontrolujte, zda je otevřený **port 3389 RDP.** Jenom tento port musí být veřejně vystavený, takže se můžete přihlásit k virtuálnímu virtuálnímu soudu. Pak přijměte všechny výchozí hodnoty a klepněte na **tlačítko Revize+ vytvořit**.

     ![Vytvoření podokna virtuálního počítače](media/container-02.png)

4. Počkejte na dokončení nasazení (několik minut). Zpráva oznamuje, že váš virtuální počítač byl vytvořen.

5. Kliknutím na **Přejít na prostředek** přejdete do okna **Přehled** pro váš virtuální počítač.

6. Vpravo klikněte na tlačítko **Připojit.** Možnosti **Připojit k virtuálnímu počítači** se zobrazí vpravo.

7. Kliknutím na tlačítko **Stáhnout soubor RDP** stáhněte soubor RDP, který umožňuje připojení k virtuálnímu virtuálnímu virtuálnímu soudu.

8. Po dokončení stahování souboru otevřete a zadejte uživatelské jméno a heslo, které jste vytvořili pro virtuální počítače.

     > [!NOTE]
     > K přihlášení nepoužívejte firemní přihlašovací údaje. (Klient RDP předpokládá, že je můžete chtít použít. Nemáte.)

9.  Vyberte **Další volby**a vyberte přihlašovací údaje virtuálního počítače.

V tomto okamžiku virtuálního virtuálního připojení je spuštěna připojena prostřednictvím RDP. Jste přihlášeni a připraveni na další krok.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Vytvoření adresáře izolovaného prostoru a nahrání souboru ZIP

1.  Vytvořte adresář na virtuálním počítači, kde můžete nahrát ukázkové a licenční soubory. Například **C:\\Sandbox**.

2.  Nahrajte **ent\_server\_dockerfiles\_4.0\_windows.zip** a soubor **ES-Docker-Prod-XXXXXXXX.mflic** do adresáře, který jste vytvořili.

3.  Extrahujte obsah souboru ZIP do **adresáře\_systému windows\_ent server,\_\_** který byl vytvořen procesem extrakce. Tento adresář obsahuje soubor readme (jako soubor HTML a TXT) a dvě podadresáře **EnterpriseServer** a **Příklady**.

4.  Zkopírujte **ES-Docker-Prod-XXXXXXXX.mflic** do\\adresáře\\\_C: Sandbox ent server\_dockerfiles\_\_4.0\\windows\_EnterpriseServer\_\\a\\C:\\Sandbox\\ent\_server\_dockerfiles 4.0 windows Příklady adresářů CICS.

> [!NOTE]
> Ujistěte se, že zkopírujete licenční soubor do obou adresářů. Jsou vyžadovány pro krok sestavení Dockeru, abyste se ujistili, že bitové kopie jsou správně licencovány.

## <a name="check-docker-version-and-create-base-image"></a>Kontrola verze Dockeru a vytvoření základní bitové kopie

> [!IMPORTANT]
> Vytvoření příslušné image Dockeru je dvoustupňový proces. Nejprve vytvořte základní bitovou kopii podnikového serveru 4.0.Pak vytvořte další obrázek pro platformu x64. I když můžete vytvořit bit x86 (32bitový), použijte 64bitový obraz.

1. Otevřete příkazový řádek.

2. Zkontrolujte, zda je nainstalován Docker. Na příkazovém řádku zadejte:

    ```
        docker version
    ```

     Například verze byla 18.09.0, když to bylo napsáno.

3. Chcete-li změnit adresář, zadejte **příkaz cd \Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer**.

4. Zadejte **bld.bat IacceptEULA** pro zahájení procesu sestavení pro počáteční základní bitovou kopii. Počkejte několik minut, než bude tento proces spuštěn. Ve výsledcích si všimněte dvou vytvořených obrázků – jednoho pro x64 a jednoho pro x86:

     ![Příkazové okno zobrazující obrázky](media/container-04.png)

5. Chcete-li vytvořit konečnou bitovou kopii pro ukázku CICS, přepněte do adresáře CICS zadáním **cd\\Sandbox\\ent\_server\_dockerfiles\_4.0\_windows\\Příklady\\CICS**.

6. Chcete-li vytvořit obrázek, zadejte **bld.bat x64**. Počkejte několik minut na spuštění procesu a zprávy oznamovat, že obrázek byl vytvořen.

7. Zadejte **image dockeru,** chcete-li zobrazit seznam všech imitek Dockeru nainstalovaných na virtuálním počítači. Ujistěte se, že **microfocus / es-acctdemo** je jedním z nich.

     ![Příkazové okno zobrazující obrázek es-acctdemo](media/container-05.png)

## <a name="run-the-image"></a>Spuštění obrázku 

1.  Chcete-li spustit podnik Enterprise Server 4.0 a aplikaci acctdemo, zadejte na příkazovém řádku:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Nainstalujte emulátor terminálu 3270, například [x3270,](http://x3270.bgp.nu/) a použijte jej k připojení k běžícímu obrazu přes port 9040.

3.  Získejte IP adresu kontejneru acctdemo, aby Docker mohl fungovat jako server DHCP pro kontejnery, které spravuje:

    1.  Získat ID běžící kontejner. Zadejte **Docker ps** na příkazovém řádku a poznamenejte si ID (**22a0fe3159d0** v tomto příkladu). Uložte si jej na další krok.

    2.  Chcete-li získat IP adresu pro kontejner acctdemo, použijte ID kontejneru z předchozího kroku následujícím způsobem:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Například:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Poznamenejte si IP adresu obrázku acctdemo. Například adresa v následujícím výstupu je 172.19.202.52.

     ![Příkazové okno zobrazující IP adresu](media/container-06.png)

5. Připojte obraz pomocí emulátoru. Nakonfigurujte emulátor tak, aby používal adresu image acctdemo a portu 9040. Tady je **172.19.202.52:9040**. Tvůj bude podobný. Otevře se obrazovka **Přihlášení do CICS.**

    ![Obrazovka Přihlášení k CICS](media/container-07.png)

6. Přihlaste se k oblasti CICS zadáním **sysad** pro **USERID** a **SYSAD** pro **heslo**.

7. Vymažte obrazovku pomocí klávesové mapy emulátoru. Pro x3270 vyberte volbu nabídky **Mapa kláves.**

8. Chcete-li spustit aplikaci acctdemo, zadejte **příkaz ACCT**. Zobrazí se úvodní obrazovka aplikace.

     ![Ukázková obrazovka účtu](media/container-08.png)

9. Experimentujte s typy účtů zobrazení. Například zadejte **D** pro požadavek a **11111** pro **účet**. Ostatní čísla účtů vyzkoušet, jsou 22222, 33333, a tak dále.

     ![Ukázková obrazovka účtu](media/container-09.png)

10. Chcete-li zobrazit konzolu Enterprise Server Administration console, přejděte na příkazový řádek a zadejte **příkaz start http:172.19.202.52:86**

     ![Konzola pro správu podnikového serveru](media/container-010.png)

A to je vše! Teď spouštěte a spravujete aplikaci CICS v kontejneru Dockeru.

## <a name="next-steps"></a>Další kroky

- [Instalace Micro Focus Enterprise Server 4.0 a Enterprise Developer 4.0 do Azure](./set-up-micro-focus-azure.md)
- [Migrace mainframových aplikací](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
