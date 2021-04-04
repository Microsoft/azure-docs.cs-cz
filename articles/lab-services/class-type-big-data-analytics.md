---
title: Nastavení testovacího prostředí pro učení analýz velkých objemů dat pomocí Azure Lab Services | Microsoft Docs
description: Naučte se, jak nastavit testovací prostředí pro učení analýzy velkých objemů dat pomocí nasazení Docker pro Hortonworks data Platform (HDP).
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 5eb9cd00350c41645d4427e30a6f25a6c163358c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659892"
---
# <a name="set-up-a-lab-for-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Nastavení testovacího prostředí pro analýzu velkých objemů dat pomocí nasazení Docker pro datovou platformu HortonWorks

V tomto článku se dozvíte, jak nastavit testovací prostředí pro výuku třídy pro analýzu velkých objemů dat.  U tohoto typu třídy studenti zjistí, jak zpracovávat velké objemy dat a použít algoritmy strojového a statistického učení k odvození přehledů dat.  Klíčový cíl pro studenty je Naučte se používat nástroje pro analýzu dat, jako je [Open source softwarový balíček Apache Hadoop](https://hadoop.apache.org/) , který poskytuje nástroje pro ukládání, správu a zpracování velkých objemů dat.

V tomto testovacím prostředí studenti použijí oblíbenou komerční verzi Hadoop poskytovanou [Cloudera](https://www.cloudera.com/), která se nazývá [Hortonworks data Platform (HDP)](https://www.cloudera.com/products/hdp.html).  Konkrétně studenti budou používat [HDP Sandbox 3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) , což je zjednodušená a snadno použitelná verze platformy, která je nenákladná a určená ke studiu a experimentování.  I když tato třída může používat virtuální počítače se systémem Windows nebo Linux s nasazeným HDP izolovaným prostorem (sandbox), v tomto článku se dozvíte, jak používat Windows.

Dalším zajímavým aspektem tohoto testovacího prostředí je, že nasadíme HDP Sandbox na virtuálních počítačích testovacího prostředí pomocí kontejnerů [Docker](https://www.docker.com/) .  Každý kontejner Docker poskytuje vlastní izolované prostředí pro softwarové aplikace, které se mají spustit v rámci.  Kontejnery Docker jsou podobně jako vnořené virtuální počítače a dají se použít k snadnému nasazení a spuštění nejrůznějších softwarových aplikací na základě imagí kontejnerů, které jsou k dispozici v [Docker Hub](https://www.docker.com/products/docker-hub).  Skript nasazení Cloudera pro HDP izolovaný prostor (sandbox) automaticky načte [Image izolovaného prostoru HDP 3.0.1 Docker](https://hub.docker.com/r/hortonworks/sandbox-hdp) z dokovacího centra a spustí dva kontejnery Docker:
  - izolovaný prostor – HDP
  - izolovaný prostor – proxy

## <a name="lab-configuration"></a>Konfigurace testovacího prostředí

K nastavení tohoto testovacího prostředí potřebujete předplatné Azure a účet testovacího prostředí, abyste mohli začít. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/). Po získání předplatného Azure můžete vytvořit nový účet testovacího prostředí v Azure Lab Services. Další informace o vytvoření nového účtu testovacího prostředí najdete v [kurzu nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).  Můžete použít i existující účet testovacího prostředí.

### <a name="lab-account-settings"></a>Nastavení účtu testovacího prostředí

Pro účet testovacího prostředí povolte nastavení popsaná v následující tabulce. Další informace o tom, jak povolit image Marketplace, najdete v tématu [určení imagí z Marketplace dostupných pro tvůrce testovacích prostředí](./specify-marketplace-images.md).

| Nastavení účtu testovacího prostředí | Pokyny |
| ------------------- | ------------ |
|Image z Marketplace| Povolte image Windows 10 pro pro použití v rámci vašeho účtu testovacího prostředí.|

### <a name="lab-settings"></a>Nastavení testovacího prostředí

Při nastavování testovacího prostředí učebny použijte nastavení v následující tabulce.  Další informace o tom, jak vytvořit prostředí učebny, najdete v tématu [Nastavení kurzu pro prostředí učebny](tutorial-setup-classroom-lab.md).

| Nastavení testovacího prostředí | Hodnota/pokyny |
| ------------ | ------------------ |
|Velikost virtuálního počítače| Střední (vnořená virtualizace). Tato velikost virtuálního počítače se nejlépe hodí pro relační databáze, ukládání do mezipaměti v paměti a analýzy.  Tato velikost také podporuje vnořenou virtualizaci.|  
|Image virtuálního počítače| Windows 10 Pro|

> [!NOTE] 
> Musíme použít střední (vnořenou virtualizaci), protože nasazením izolovaného prostoru (HDP) pomocí Docker vyžaduje:
>   - Windows Hyper-V s vnořenou virtualizací
>   - Aspoň 10 GB paměti RAM

## <a name="template-machine-configuration"></a>Konfigurace počítače šablony

Pro nastavení počítače šablony budeme:
- Instalace Dockeru
- Nasazení izolovaného prostoru (HDP)
- Použití PowerShellu a Windows Plánovač úloh k automatickému spuštění kontejnerů Docker

### <a name="install-docker"></a>Instalace Dockeru

Kroky v této části jsou založené na [pokynech Cloudera pro nasazení pomocí kontejnerů Docker](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html). 

Pokud chcete použít kontejnery Docker, musíte nejdřív nainstalovat Docker Desktop na virtuálním počítači šablony:

1. Pomocí postupu v [části požadavky](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) nainstalujte [Docker for Windows](https://docs.docker.com/docker-for-windows/install/). 

    > [!IMPORTANT] 
    > Zajistěte, aby možnost **použít kontejnery Windows namísto pro konfiguraci kontejnerů systému Linux** nebyla zaškrtnuta.

1. Zajistěte, aby byly zapnuté **kontejnery Windows a funkce technologie Hyper-V** .
   ![Zapnout nebo vypnout funkce systému Windows](./media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Pokud chcete nakonfigurovat konfiguraci paměti Docker, postupujte podle kroků v části [paměť pro systém Windows](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) .

    > [!WARNING]
    > Pokud nechtěně zkontrolujete možnost **použít kontejnery Windows namísto kontejnerů Linux** při instalaci Docker, neuvidíte nastavení konfigurace paměti.  Pokud to chcete opravit, můžete přepnout na používání kontejnerů Linux [kliknutím na ikonu Docker v hlavním panelu systému Windows](https://docs.docker.com/docker-for-windows/#docker-settings-dialog). Když se otevře nabídka Docker Desktop, vyberte **Přepnout na kontejnery Linux**.
 
### <a name="deploy-hdp-sandbox"></a>Nasazení izolovaného prostoru (HDP)

V této části nasadíte HDP izolovaný prostor (sandbox) a pak získáte přístup k izolovanému prostoru HDP pomocí prohlížeče.

1. Ujistěte se, že jste nainstalovali [Git bash](https://gitforwindows.org/) , jak je uvedeno v [části požadavky](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) v průvodci, protože se to doporučuje pro dokončení dalších kroků.

1. Pomocí [Průvodce nasazením a instalací Cloudera pro Docker](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)proveďte kroky v následujících částech:
   
   -    Nasazení izolovaného prostoru (HDP)
   -    Ověřit izolovaný prostor HDP

    > [!WARNING] 
    > Když si stáhnete nejnovější soubor. zip pro HDP, ujistěte se, že *neuložíte* soubor. zip do cesty k adresáři, který obsahuje prázdné znaky.

    > [!NOTE] 
    > Pokud se během nasazování zobrazí výjimka s informacemi o tom, že nebyla **sdílena jednotka**, je nutné sdílet jednotku C s Docker, aby měly kontejnery HDP Linux přístup k místním souborům Windows.  Pokud to chcete opravit, [klikněte na ikonu Docker v panelu systému Windows](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) a otevřete nabídku Docker Desktop a vyberte **Nastavení**.  Když se otevře dialogové okno **Nastavení Docker** , vyberte **prostředky > sdílení souborů** a ověřte jednotku **C** .  Pak můžete zopakovat postup nasazení izolovaného prostoru (HDP).

1. Jakmile jsou kontejnery Docker pro izolovaný prostor (sandbox) HDP nasazené a spuštěné, můžete k prostředí přistupovat tak, že spustíte prohlížeč a následující pokyny Cloudera pro otevření [uvítací stránky izolovaného prostoru](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) a spuštění řídicího panelu HDP.

    > [!NOTE] 
    > Tyto pokyny předpokládají, že jste nejdřív namapovali místní IP adresu prostředí izolovaného prostoru (sandbox) na sandbox-hdp.hortonworks.com v hostitelském souboru na vašem VIRTUÁLNÍm počítači šablony.  Pokud toto mapování **neprovedete** , můžete přejít na uvítací stránku izolovaného prostoru, a to tak, že přejdete na `http://localhost:8080` .

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Automatické spuštění kontejnerů Docker při přihlášení studentů

K zajištění snadno použitelného prostředí pro studenty použijeme PowerShellový skript, který automaticky:
  - Spustí kontejnery Docker izolovaného prostoru (HDP), když se spustí student a připojí se ke svému testovacímu virtuálnímu počítači.
  - Spustí prohlížeč a přejde na úvodní stránku izolovaného prostoru (sandbox).
K automatickému spuštění tohoto skriptu používáme také Windows Plánovač úloh, když se student do svého virtuálního počítače přihlásí.
Chcete-li nastavit tento postup, postupujte následovně: [skriptování analýzy velkých](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/)objemů dat.

## <a name="cost-estimate"></a>Odhad nákladů

Pokud byste chtěli odhadnout náklady na toto testovací prostředí, můžete použít následující příklad.

Pro třídu 25 studentů s 20 hodinami plánovaného času třídy a 10 hodin pro domácí úlohy nebo přiřazení by cena za testovací prostředí byla:
  - 25 studentů * (20 + 10) hodin × 55 jednotek testovacího prostředí × 0,01 USD za hodinu = 412,50 USD

Další podrobnosti o cenách najdete v článku [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Závěr

Tento článek vás vás provedl kroky potřebnými k vytvoření testovacího prostředí pro třídu analýzy velkých objemů dat, která používá datovou platformu Hortonworks nasazenou s Docker.  Nastavení pro tento typ třídy lze použít pro podobné třídy datové analýzy.  Toto nastavení může být použito i pro jiné typy tříd, které používají Docker pro nasazení.

## <a name="next-steps"></a>Další kroky

Další kroky jsou běžné pro nastavení testovacího prostředí.

- [Vytvoření a Správa šablony](how-to-create-manage-template.md)
- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users)