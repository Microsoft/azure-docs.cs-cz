---
title: Provozování Micro Enterprise serveru 5,0 v kontejneru Docker v Azure | Microsoft Docs
description: V tomto článku se dozvíte, jak spustit Micro Enterprise Server 5,0 v kontejneru Docker na Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: bfd40d39907c4e69ded0fa257305d346ca261836
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319992"
---
# <a name="run-micro-focus-enterprise-server-50-in-a-docker-container-on-azure"></a>Provozování Micro Enterprise serveru 5,0 v kontejneru Docker v Azure

Micro Micro Enterprise Server 5,0 můžete spustit v kontejneru Docker v Azure. V tomto kurzu se dozvíte, jak. Používá Windows CICS (Customer Information Control System) acctdemo pro podnikový server.

Docker přidá do aplikací přenositelnost a izolaci. Můžete například exportovat Docker image z jednoho virtuálního počítače s Windows a spustit ho na jiném nebo z úložiště na Windows Server s Docker. Image Docker se spouští v novém umístění se stejnou konfigurací, aniž by bylo nutné instalovat podnikový server. Je součástí obrázku. Licenční požadavky se pořád týkají.

V tomto kurzu se do **Windows 2016 Datacenter** nainstaluje virtuální počítač s kontejnery z Azure Marketplace. Tento virtuální počítač obsahuje **Docker 18.09.0**. Následující postup ukazuje, jak nasadit kontejner, spustit ho a pak se k němu připojit pomocí emulátoru 3270.

## <a name="prerequisites"></a>Požadavky

Než začnete, podívejte se na tyto požadavky:

-   Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

-   Software zaměřený na Micro a platnou licenci (nebo zkušební licenci). Pokud jste stávajícím zákazníkem s fokusem, obraťte se na svého zástupce Micro Focus. V opačném případě [požádejte o zkušební verzi](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

    > [!Note] 
    > Ukázkové soubory Docker jsou součástí podnikového serveru 5,0. V tomto kurzu se používáwindows.zip s využitím rozlehlého \_ serveru \_ fázemi \_ 5,0 \_ . Můžete k němu přistupovat ze stejného místa, kde jste získali přístup k instalačnímu souboru podnikového serveru, nebo přejít na *Micro Focus* a začít.

-   Dokumentace k [podnikovým serverům a podnikovým vývojářům](https://www.microfocus.com/documentation/enterprise-developer/#%22)

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1.  Zabezpečte médium ze \_ \_ \_ souboru fázemi 5,0 \_windows.zipho serveru. Zabezpečte soubor licencí ES-Docker-prod-XXXXXXXX. mflic (potřebný k sestavení imagí Docker).

2.  Vytvořte virtuální počítač. Provedete to tak, že otevřete Azure Portal, v levém horním rohu vyberete **vytvořit prostředek** a vyfiltrujete ho podle *operačního systému Windows Server*. Ve výsledcích vyberte možnost **Windows Server.** Na další obrazovce vyberte **Windows Server 2016 Datacenter – s kontejnery**.

    ![Snímek obrazovky s výsledky hledání Azure Portal](./media/run-image-1.png)

3.  Pokud chcete nakonfigurovat vlastnosti pro virtuální počítač, vyberte podrobnosti instance:

    1.  Vyberte velikost virtuálního počítače. Pro účely tohoto kurzu zvažte použití standardního virtuálního počítače **DS2 \_ V3** se 2 VCPU a 16 GB paměti.

    2.  Vyberte **oblast** a **skupinu prostředků** , do které chcete nasadit.

    3.  Pro **Možnosti dostupnosti**použijte výchozí nastavení.

    4.  Jako **uživatelské jméno**zadejte účet správce, který chcete použít, a heslo.

    5.  Ujistěte se, že je **port 3389 RDP** otevřený. Pouze tento port musí být veřejně vystavený, takže se můžete přihlásit k virtuálnímu počítači. Pak přijměte všechny výchozí hodnoty a klikněte na tlačítko **zkontrolovat + vytvořit**.

    ![Snímek obrazovky s podoknem vytvořit virtuální počítač](./media/run-image-2.png)

4.  Počkejte na dokončení nasazení (několik minut). Zobrazí se zpráva, že byl virtuální počítač vytvořen.

5.  Výběrem možnosti **Přejít k prostředku** přejdete do okna **Přehled** pro váš virtuální počítač.

6.  Na pravé straně vyberte **připojit**. Na pravé straně se zobrazí možnosti **připojit k virtuálnímu počítači** .

7.  Kliknutím na tlačítko **Stáhnout soubor protokolu RDP** Stáhněte soubor protokolu RDP (Remote Desktop Protocol), který vám umožní připojit se k virtuálnímu počítači.

8.  Po dokončení stahování souboru ho otevřete a zadejte uživatelské jméno a heslo, které jste vytvořili pro virtuální počítač.

    > [!Note]    
    > Nepoužívejte své podnikové přihlašovací údaje pro přihlášení. (Klient RDP předpokládá, že je budete chtít použít. Nebudete.)

9.  Vyberte **Další volby**a pak vyberte přihlašovací údaje k virtuálnímu počítači.

V tomto okamžiku je virtuální počítač spuštěný a připojený přes RDP. Jste přihlášení a připraveni k dalšímu kroku.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Vytvoření adresáře izolovaného prostoru (sandbox) a nahrání souboru ZIP

1.  Na virtuálním počítači vytvořte adresář, do kterého můžete nahrát ukázku a licenční soubory. Například **C: \\ Sandbox**.

2.  Nahrajte ** \_ \_ fázemi \_ 5,0 \_windows.zip** a soubor **ES-Docker-prod-xxxxxxxx. mflic** do adresáře, který jste vytvořili.

3.  Extrahujte obsah souboru zip do adresáře ** \_ \_ fázemi \_ 5,0 \_ systému Windows** vytvořeného procesem extrakce. Tento adresář obsahuje soubor Readme (jako soubor. html a. txt) a dva podadresáře, **EnterpriseServer** a **Příklady**.

4.  Zkopírujte **ES-Docker-prod-xxxxxxxx. mflic** do C: \\ izolovaného prostoru (sandbox) \\ \_ \_ fázemi \_ 5,0 \_ Windows \\ EnterpriseServer a c: \\ izolovaný Server izolovaného \\ \_ serveru \_ fázemi \_ 5,0 \_ Windows \\ Příklady \\ CICS adresářů.  
      
    > [!Note]
    > Nezapomeňte zkopírovat soubor licencí do obou adresářů. Jsou vyžadovány pro krok sestavení Docker, aby bylo zajištěno, že jsou bitové kopie správně licencovány.

## <a name="check-docker-version-and-create-base-image"></a>Podívejte se na verzi Docker a vytvořte základní image.

> [!Important]  
> Vytvoření vhodné image Docker je proces se dvěma kroky. Nejdřív vytvořte základní image Enterprise Server 5,0. Pak vytvořte další image pro platformu x64. I když můžete vytvořit bitovou kopii x86 (32), použijte bitovou kopii 64.

1.  Otevřete příkazový řádek.

2.  Ověřte, že je Docker nainstalovaný. Do příkazového řádku zadejte: **verze Docker**  
    Například při zápisu verze byla 18.09.0.

3.  Chcete-li změnit adresář, zadejte:  
    **disk CD \\ Izolovaný Server izolovaného prostoru \\ \_ \_ fázemi \_ 5,0 \_ Windows \\ EnterpriseServer**.

4.  Zadejte **bld.bat IacceptEULA** a zahajte proces sestavení pro počáteční základní bitovou kopii. Počkejte několik minut, než se tento proces spustí. Ve výsledcích si všimněte dvou vytvořených imagí – jeden pro platformu x64 a jeden pro x86:

    ![okno Příkaz zobrazování imagí](./media/run-image-3.png)

5.  Pokud chcete vytvořit finální image pro ukázku CICS, přepněte do adresáře CICS zadáním fázemi ** \\ sandboxu pro \\ \_ \_ \_ Windows 5,0 \_ Windows \\ Examples \\ CICS**.

6.  Chcete-li vytvořit bitovou kopii, zadejte **bld.bat x64**. Počkejte několik minut, než se proces spustí, a zobrazí se zpráva oznamující, že se image vytvořila.

7.  Chcete-li zobrazit seznam všech imagí Docker nainstalovaných na virtuálním počítači, zadejte **Docker images** . Zajistěte, aby byl jeden z nich **mikrofocus/ES-acctdemo** .

    ![Okno Příkaz zobrazení obrázku ES-acctdemo](./media/run-image-4.png)

## <a name="run-the-image"></a>Spustit bitovou kopii

1.  Pokud chcete spustit podnikový server 5,0 a aplikaci acctdemo, zadejte na příkazovém řádku tento příkaz:

    ```
    **docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win\_5.0\_x64
    ```

1.  Nainstalujte emulátor terminálu 3270, například [x3270](http://x3270.bgp.nu/) , a použijte ho k připojení přes port 9040 k imagi, která je spuštěná.

2.  Získejte IP adresu kontejneru acctdemo, takže Docker může fungovat jako server DHCP (Dynamic Host Configuration Protocol) pro kontejnery, které spravuje:

    1.  Získá ID běžícího kontejneru. Do příkazového řádku zadejte **Docker PS** a poznamenejte si ID (v tomto příkladu**22a0fe3159d0** ). Uložte si ho pro další krok.

    2.  K získání IP adresy pro kontejner acctdemo použijte ID kontejneru z předchozího kroku následujícím způsobem:

    ```
    docker inspect \<containerID\> --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

    Příklad:

    ```
    docker inspect 22a0fe3159d0 --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

4. Poznamenejte si IP adresu pro obrázek acctdemo. Například adresa v následujícím výstupu je 172.19.202.52.

    ![Snímek obrazovky okno Příkaz se zobrazením IP adresy](./media/run-image-5.png)

5. Připojte Image pomocí emulátoru. Nakonfigurujte emulátor tak, aby používal adresu acctdemo image a port 9040. Tady je **172.19.202.52:9040**. Vaše moje akce bude podobná. Otevře se obrazovka **přihlášení k CICS** .

    ![Snímek obrazovky s jednotného a CICS](./media/run-image-6.png)

6. Přihlaste se k CICS oblasti tak, že zadáte **SYSAD** pro **UserID** a **SYSAD** pro **heslo**.

7. Vymažte obrazovku pomocí keymap emulátoru. Pro x3270 vyberte možnost nabídky **keymap** .

8. Chcete-li spustit aplikaci acctdemo, zadejte **Acct**. Zobrazí se úvodní obrazovka aplikace.

     ![Snímek obrazovky s ukázkou účtu](./media/run-image-7.png)

9. Experimentujte s typy účtů zobrazení. Například zadejte **D** pro požadavek a **11111** pro **účet**. Další čísla účtů k vyzkoušení jsou 22222, 33333 a tak dále.

    ![Snímek obrazovky s ukázkou účtu](./media/run-image-8.png)

10. Pokud chcete zobrazit konzolu pro správu podnikového serveru, otevřete příkazový řádek a zadejte příkaz **Start http: 172.19.202.52:86**.

    ![Konzola pro správu podnikového serveru](media/run-image-9.png)

A to je vše! Teď spouštíte a spravujete aplikaci CICS v kontejneru Docker.

## <a name="next-steps"></a>Další kroky

-   [Instalace Micro Micro Enterprise Server 5,0 a Enterprise Developer 5,0 v Azure](./set-up-micro-focus-azure.md)

-   [Migrace mainframových aplikací](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
