---
title: Nastavení testovacího prostředí pro výuku analýzy velkých objemů dat pomocí služby Azure Lab Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak nastavit testovací prostředí pro výuku analýzy velkých objemů dat pomocí dockerového nasazení datové platformy Hortonworks (HDP).
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/15/2020
ms.author: nicolela
ms.openlocfilehash: c499b7a0f1cd16bf57fef21742b01bda71249916
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538775"
---
# <a name="set-up-a-lab-to-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Nastavení testovacího prostředí pro analýzu velkých objemů dat pomocí dockerového nasazení datové platformy HortonWorks

V tomto článku se ukazuje, jak nastavit testovací prostředí pro výuku třídy analýzy velkých objemů dat.  U tohoto typu třídy se studenti učí, jak zpracovat velké objemy dat a jak použít algoritmy strojového a statistického učení k odvození dat.  Klíčovým cílem pro studenty je naučit se používat nástroje pro analýzu dat, jako je [například open-source softwarový balíček Apache Hadoop,](https://hadoop.apache.org/) který poskytuje nástroje pro ukládání, správu a zpracování velkých objemů dat.

V této laboratoři budou studenti používat populární komerční verzi Hadoopu poskytovanou [společností Cloudera](https://www.cloudera.com/), nazvanou [Hortonworks Data Platform (HDP).](https://www.cloudera.com/products/hdp.html)  Konkrétně studenti budou používat [HDP Sandbox 3.0.1,](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) což je zjednodušená, snadno použitelná verze platformy, která je zdarma a je určena pro učení a experimentování.  Přestože tato třída může používat virtuální počítače windows nebo linux (VM) s HDP sandbox nasazen, tento článek ukáže, jak používat Windows.

Dalším zajímavým aspektem této laboratoře je, že nasadíme hdp sandbox na virtuálních počítačích testovacího prostředí pomocí kontejnerů [Dockeru.](https://www.docker.com/)  Každý kontejner Dockeru poskytuje vlastní izolované prostředí pro softwarové aplikace, které mají být spuštěny uvnitř.  Koncepčně jsou kontejnery Dockeru jako vnořené virtuální počítače a lze je snadno nasadit a spustit širokou škálu softwarových aplikací založených na inamitách kontejnerů poskytovaných v [Docker Hubu](https://www.docker.com/products/docker-hub).  Skript nasazení Cloudera pro hdp sandbox automaticky vytáhne [image Dockeru HDP 3.0.1](https://hub.docker.com/r/hortonworks/sandbox-hdp) z Docker Hubu a spustí dva kontejnery Dockeru:
  - pískoviště-hdp
  - server s pískovištěm

## <a name="lab-configuration"></a>Konfigurace laboratoře

Chcete-li nastavit toto testovací prostředí, budete potřebovat předplatné Azure a účet testovacího prostředí, abyste mohli začít. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete. Jakmile získáte předplatné Azure, můžete si vytvořit nový účet testovacího prostředí ve službách Azure Lab Services. Další informace o vytvoření nového účtu testovacího prostředí naleznete [v tématu Výuka nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).  Můžete také použít existující účet testovacího prostředí.

### <a name="lab-account-settings"></a>Nastavení účtu laboratoře

Povolte nastavení popsaná v následující tabulce pro účet testovacího prostředí. Další informace o povolení bitových kopií marketplace najdete [v tématu Určení bitových kopií marketplace dostupných tvůrcům testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

| Nastavení účtu laboratoře | Pokyny |
| ------------------- | ------------ |
|Obrázek tržiště| Povolte bitovou kopii Windows 10 Pro pro použití v rámci svého účtu testovacího prostředí.|

### <a name="lab-settings"></a>Nastavení laboratoře

Při nastavování učební laboratoře použijte nastavení v následující tabulce.  Další informace o vytvoření učebny naleznete v [tématu nastavení výukového programu pro testovací prostředí ve třídě](tutorial-setup-classroom-lab.md).

| Nastavení laboratoře | Hodnota/instrukce |
| ------------ | ------------------ |
|Velikost virtuálního počítače| Střední (vnořená virtualizace). Tato velikost virtuálního počítače je nejvhodnější pro relační databáze, ukládání do mezipaměti v paměti a analýzy.  Tato velikost také podporuje vnořenou virtualizaci.|  
|Image virtuálního počítače| Windows 10 Pro|

> [!NOTE] 
> Musíme použít střední (vnořené virtualizace), protože nasazení HDP sandbox pomocí Dockervyžaduje:
>   - Windows Hyper-V s vnořenou virtualizací
>   - Alespoň 10 GB paměti RAM

## <a name="template-machine-configuration"></a>Konfigurace počítače šablony

Chcete-li nastavit šablonu stroje, budeme:
- Instalace Dockeru
- Nasazení izolovaného prostoru HDP
- Automatické spuštění kontejnerů Dockeru pomocí Plánovače úloh prostředí PowerShell a Windows

### <a name="install-docker"></a>Instalace Dockeru

Kroky v této části jsou založeny na [pokynech Cloudera pro nasazení s kontejnery Dockeru](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html). 

Pokud chcete použít kontejnery Dockeru, musíte nejdřív nainstalovat Docker Desktop na virtuální počítač šablony:

1. Podle pokynů v [části Požadavky](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) nainstalujte Docker pro [Windows](https://docs.docker.com/docker-for-windows/install/). 

    > [!IMPORTANT] 
    > Ujistěte se, že **použití kontejnerů Windows namísto konfigurace kontejnerů Linuxu** je ponecháno nezaškrtnuté.

1. Ujistěte se, že jsou **zapnuté kontejnery Windows a funkce Technologie Hyper-V.**
   ![Zapnout nebo vypnout funkce systému Windows](../media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Podle pokynů v části [Paměť pro Windows](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) nakonfigurujte konfiguraci paměti Dockeru.

    > [!WARNING]
    > Pokud neúmyslně zkontrolujete **možnost Použít kontejnery Windows namísto linuxových kontejnerů** při instalaci Dockeru, neuvidíte nastavení konfigurace paměti.  Chcete-li tento problém vyřešit, můžete přepnout na používání kontejnerů Linux [kliknutím na ikonu Dockeru v systémové liště systému Windows](https://docs.docker.com/docker-for-windows/#docker-settings-dialog); po otevření nabídky Docker Desktop vyberte **Přepnout na linuxové kontejnery**.
 
### <a name="deploy-hdp-sandbox"></a>Nasazení izolovaného prostoru HDP

V této části nasadíte HDP sandbox a pak také přístup HDP sandbox pomocí prohlížeče.

1. Ujistěte se, že jste nainstalovali [Git Bash,](https://gitforwindows.org/) jak je uvedeno v [části Požadavky](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) průvodce, protože je doporučeno pro dokončení dalších kroků.

1. Pomocí [průvodce nasazením a instalací cloudery pro Docker](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)proveďte kroky v následujících částech:
   
   -    Nasazení izolovaného prostoru HDP
   -    Ověřit hdp sandbox

    > [!WARNING] 
    > Při stahování nejnovějšího souboru ZIP pro hdp se ujistěte, že soubor ZIP *neuložíte* do cesty k adresáři, která obsahuje mezery.

    > [!NOTE] 
    > Pokud se během nasazení zobrazí výjimka, která uvádí, že **jednotka nebyla sdílena**, musíte sdílet jednotku C s Dockerem, aby kontejnery HDP linux u měly přístup k místním souborům systému Windows.  Chcete-li tento problém vyřešit, [kliknutím na ikonu Dockeru na hlavním panelu systému Windows](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) otevřete nabídku Plochy Dockeru a vyberte **Nastavení**.  Když se otevře dialogové okno **Nastavení Dockeru,** vyberte **Zdroje > sdílení souborů** a zkontrolujte jednotku **C.**  Potom můžete zopakovat kroky k nasazení HDP sandbox.

1. Jakmile jsou kontejnery Dockeru pro HDP Sandbox nasazeny a spuštěny, můžete přistupovat k prostředí spuštěním prohlížeče a podle pokynů Cloudera pro otevření [uvítací stránky izolovaného prostoru](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) a spuštění řídicího panelu HDP.

    > [!NOTE] 
    > Tyto pokyny předpokládají, že jste nejprve namapovali místní IP adresu prostředí izolovaného prostoru na sandbox-hdp.hortonworks.com v hostitelském souboru na virtuálním počítači šablony.  Pokud toto mapování **neučiníte,** můžete se na úvodní stránku izolovaného prostoru dostat na úvodní stránku izolovaného prostoru na . [http://localhost:8080](http://localhost:8080)

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Automatické spouštění kontejnerů Dockeru, když se studenti přihlásí

Abychom studentům zajistili snadné využití, použijeme skript PowerShellu, který automaticky:
  - Spustí kontejnery HDP Sandbox Docker, když student spustí a připojí se k jejich virtuálního virtuálního uživatele testovacího prostředí.
  - Spustí prohlížeč a přejde na úvodní stránku izolovaného prostoru.
Plánovač úloh systému Windows také použijeme k automatickému spuštění tohoto skriptu, když se student přihlásí k virtuálnímu virtuálnímu provozu.
Chcete-li toto nastavení nastavit, postupujte takto: [Skriptování služby Big Data Analytics](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/).

## <a name="cost-estimate"></a>Odhad nákladů

Pokud chcete odhadnout náklady na tuto testovací prostředí, můžete použít následující příklad.

Pro třídu 25 studentů s 20 hodinami plánovaného času ve třídě a 10 hodinami kvóty pro domácí úkoly nebo úkoly by cena za laboratoř byla:
  - 25 studentů * (20 + 10) hodin * 55 laboratorních jednotek * 0.01 USD za hodinu = 412.50 USD

Další podrobnosti o cenách najdete v tématu [Ceny služeb Azure Lab](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Závěr

Tento článek vás provede kroky nezbytné k vytvoření testovacího prostředí pro třídu analýzy velkých objemů dat, která používá datovou platformu Hortonworks nasazenou s Dockerem.  Nastavení pro tento typ třídy lze použít pro podobné třídy analýzy dat.  Toto nastavení může být také použitelné pro jiné typy tříd, které používají Docker pro nasazení.

## <a name="next-steps"></a>Další kroky

Další kroky jsou společné pro nastavení libovolného testovacího prostředí.

- [Vytvoření a správa šablony](how-to-create-manage-template.md)
- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavení plánu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mailové registrační odkazy pro studenty](how-to-configure-student-usage.md#send-invitations-to-users)
