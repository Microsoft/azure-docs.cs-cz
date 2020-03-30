---
title: Průvodce zrychleným nastavením laboratoře pro učebny pro Azure Lab Services
description: Tato příručka pomáhá tvůrcům testovacího prostředí rychle nastavit testovací účet pro použití v rámci své školy.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: d58e11551c2857c269e8985e81f84138f6d389ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370814"
---
# <a name="classroom-lab-setup-guide"></a>Průvodce nastavením učebny v laboratoři

Proces publikování testovacího prostředí pro vaše studenty může trvat až několik hodin v závislosti na počtu virtuálních počítačů (VM), které se vytvoří ve vaší laboratoři.  Měli byste si dát alespoň jeden den na nastavení laboratoře, aby bylo zajištěno, že funguje správně a poskytnout dostatek času na publikování virtuálních her studentů.

## <a name="understand-your-classs-lab-requirements"></a>Seznamte se s laboratorními požadavky vaší třídy

Před nastavením nové laboratoře byste měli zvážit následující otázky:

**Jaké požadavky na software má třída?**

Na základě cílů výuky vaší třídy byste se měli rozhodnout, který operační systém, aplikace, nástroje atd.   Pokud chcete nastavit virtuální aplikace testovacího prostředí, máte tři možnosti:

- **Použití image Azure Marketplace** – Marketplace poskytuje stovky ibi, které můžete použít při vytváření testovacího prostředí.  Pro některé třídy může obrázek tržiště již obsahovat vše, co potřebujete pro vaši třídu.

- **Vytvořte si novou vlastní image** – můžete si vytvořit vlastní image pomocí image tržiště jako výchozího bodu a přizpůsobením instalací dalšího softwaru, provedením změn konfigurace atd.

- **Použití existujícího vlastního obrázku** – můžete znovu použít existující vlastní obrázky, které jste dříve vytvořili nebo které byly vytvořeny jinými správci nebo fakultou ve vaší škole; To vyžaduje, aby správci nakonfigurovali galerii sdílených obrázků, která je úložištěm pro ukládání vlastních bitových kopií.

> [!NOTE]
> Vaši správci jsou zodpovědní za povolení Marketplace a vlastních bitových kopií, abyste je mohli používat. budete muset koordinovat se svým oddělením IT, abyste zajistili, že obrázky, které potřebujete, jsou povoleny.  Vlastní obrázky, které vytvoříte, jsou automaticky povoleny pro použití v testovacích prostředích, které vlastníte.

**Jaké hardwarové požadavky má třída?**

Existuje celá řada velikostí výpočetních prostředků, které můžete vybrat z toho, že zahrnuje:

- Vnořené velikosti virtualizace, takže můžete studentům udělit přístup k virtuálnímu počítači, který je schopný hostování více vnořených virtuálních počítačů; Tato velikost výpočetních prostředků se například často používá pro kurzy sítě.

- Velikosti GPU, takže vaši studenti mohou používat typy aplikací náročných na počítače, například pro umělou inteligenci a strojové učení.

Úplný seznam dostupných velikostí výpočetních prostředků najdete v příručce k [velikosti virtuálních mís.](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing)

> [!NOTE]
> V závislosti na oblasti, ve které bude vaše testovací prostředí vytvořeno, se může zobrazit méně výpočetních velikostí, protože se to liší podle oblasti.  Naše obecné doporučení je vybrat nejmenší výpočetní velikost, která je nejblíže vašim potřebám.  Se službou Azure Lab Services můžete v případě potřeby později nastavit nové testovací prostředí s jinou výpočetní kapacitou.

**Jaké závislosti má třída na externí Azure nebo síťové prostředky?**

Pokud vaše virtuální počítače testovacího prostředí potřebují používat externí prostředky, jako je například databáze, sdílená složka, licenční server atd., budete muset koordinovat se správci, abyste zajistili, že vaše testovací prostředí bude mít přístup k těmto prostředkům.

Pro přístup k prostředkům Azure, které *nejsou* zabezpečené virtuální sítí, pak můžete přistupovat k těmto prostředkům prostřednictvím veřejného internetu bez jakékoli další konfigurace správci.

> [!NOTE]
> Měli byste zvážit, zda můžete snížit závislosti vašeho testovacího prostředí na externí prostředky tím, že poskytuje prostředek přímo na virtuálním počítači.  Chcete-li například vyloučit potřebu číst data z externí databáze, můžete databázi nainstalovat přímo na virtuální počítač.  

**Jak budou náklady kontrolovány?**

Lab Services používá průběžný cenový model, což znamená, že platíte jenom za dobu, po kterou je spuštěný virtuální virtuální soud v testovacím prostředí.  Chcete-li řídit náklady, máte tři možnosti, které se obvykle používají ve spojení s sebou:

- **Plán** – plán umožňuje automaticky řídit, když se virtuální počítače vašich testovacích prostředí spustí a vypnou.
- **Kvóta** – kvóta určuje počet hodin, které studenti budou mít přístup k virtuálnímu virtuálnímu účtu mimo naplánované hodiny.  Pokud je dosaženo kvóty, když ji student používá, virtuální počítač se automaticky vypne a student nebude moct restartovat virtuální počítač, pokud se kvóta nezvýší.
- **Automatické vypnutí** – Pokud je povoleno, nastavení automatického vypnutí způsobí, že virtuální počítače systému Windows se automaticky vypnou po určité době, jakmile se student odpojí od relace RDP.  Standardně je toto nastavení zakázáno.  

    > [!NOTE]
    > Toto nastavení v současné době existuje pouze pro systém Windows.

**Jak studenti zachrání svou práci?**

Studentům je přiřazen vlastní virtuální virtuální mkv, který je jim přiřazen po dobu životnosti testovacího prostředí.  Mohou si vybrat, zda:

- Uložte přímo do virtuálního virtuálního mísu.
- Ukládat do externího úložiště, jako je OneDrive, GitHub atd.

Pokud chcete používat OneDrive, můžete to nakonfigurovat automaticky pro studenty na jejich testovacích virtuálních počítačích.  Další informace o tom jsou uvedeny níže.

> [!NOTE]
> Chcete-li zajistit, aby vaši studenti měli i nadále přístup k uložené práci mimo laboratoř, která zahrnuje po ukončení kurzu, doporučujeme studentům uložit svou práci do externího úložiště.

**Jak se studenti připojí ke svému virtuálnímu virtuálnímu virtuálnímu zařízení?**

Pro rdp na virtuální počítače se systémem Windows doporučujeme studentům používat [klienta Vzdálené plochy společnosti Microsoft](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  Klient vzdálené plochy podporuje Macy, Chromebooky a Windows.

Pro virtuální počítače s Linuxem mohou studenti používat SSH nebo RDP.   Chcete-li se připojit pomocí programu RDP, zodpovídáte za instalaci a konfiguraci potřebných balíčků RDP a GUI.  Další podrobnosti o tomto je uvedeno níže.

## <a name="set-up-your-lab"></a>Nastavení testovacího prostředí

Jakmile porozumíte požadavkům na laboratoř vaší třídy, jste připraveni ji nastavit.  Podle odkazů v této části se podívejte, jak nastavit testovací prostředí.

1. **Vytvoření testovacího prostředí**

   Pokyny najdete v kurzu [o vytvoření učebny.](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab)

    > [!NOTE]
    > Pokud vaše třída vyžaduje vnořenou virtualizaci, podívejte se na kroky v návodu, který ukazuje [povolení vnořené virtualizace](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm).

1. **Přizpůsobení bitových kopií a publikování virtuálních dálužik testovacího prostředí**

    Chcete-li přizpůsobit image a publikovat virtuální počítače v testovacím prostředí, připojíte se ke speciálnímu virtuálnímu počítači nazývanému virtuální počítač šablony; naleznete v následujících průvodcích:
    - [Vytvoření a správa virtuálního počítače šablony](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Použití galerie sdílených obrázků](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Pokud používáte systém Windows, měli byste také odkazovat na pokyny v návodu k [přípravě virtuálního počítače šablony systému Windows](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template).  Tyto pokyny obsahují postup pro nastavení OneDrivu a Office, které mohou vaši studenti používat.

1. **Správa fondu virtuálních her a kapacity**

   Kapacitu virtuálních počítačů můžete snadno vertikálně navýšit nebo snížit podle potřeby vaší třídy.  Mějte na paměti, že zvýšení kapacity virtuálních stránek může trvat několik hodin, protože to zahrnuje nastavení nových virtuálních stránek.  Postup pro [nastavení a správu fondu virtuálních her](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)najdete v návodu k nastavení a správě fondu virtuálních her .

1. **Přidání a správa uživatelů testovacího prostředí**

   Pokud chcete přidat uživatele do testovacího prostředí, podívejte se na postup v následujících kurzech:
   - [Přidání uživatelů do testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Odesílání pozvánek uživatelům](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Informace o typech účtů, které mohou studenti používat, naleznete v následujícím článku:
    - [Studentské účty](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)
  
1. **Nastavení ovládacích prvků nákladů**

    Chcete-li řídit náklady na testovací prostředí, nastavte plány, kvóty a automatické vypnutí. naleznete v následujících kurzech:

   - [Nastavení plánu](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > V závislosti na typu nainstalovaného operačního systému může spuštění virtuálního počítače trvat několik minut.  Chcete-li zajistit, že testovací virtuální počítač je připravený k použití během naplánovaných hodin, doporučujeme spustit virtuální počítače 30 minut předem, abyste zajistili, že virtuální počítače jsou spuštěné a připravené k použití.

   - [Nastavení kvót pro uživatele](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) a [nastavení další kvóty pro konkrétního uživatele](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quota-for-a-specific-user)
  
   - [Povolení automatického vypnutí při odpojení](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Plány, kvóty a automatické vypnutí *se nevztahují* na virtuální počítače šablony.  V důsledku toho je nutné zajistit, že vypnete virtuální počítače šablony, když se nepoužívá jinak bude nadále vynakládat náklady.  Ve výchozím nastavení se také při vytváření testovacího prostředí automaticky spustí virtuální virtuální počítače šablony, takže se budete chtít ujistit, že okamžitě dokončíte nastavení testovacího prostředí a vypnete virtuální virtuální počítače šablony.

1. **Použití řídicího panelu**

    Pokyny najdete v návodu k [použití řídicího panelu testovacího prostředí.](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard)

    > [!NOTE]
    > Odhadované náklady zobrazené na řídicím panelu představují maximální náklady, které můžete očekávat pro studenty, kteří jsou v testovacím prostředí.  Například vám *not* nebudou studenti účtovat nevyužité hodiny kvót.  Odhadované náklady *neodrážejí* žádné poplatky za použití virtuálního počítače šablony nebo galerie sdílených obrázků.

## <a name="next-steps"></a>Další kroky

Viz následující články:

- [Sledování využití testovacího prostředí v učebně](tutorial-track-usage.md)
  
- [Přístup k testovacímu prostředí v učebně](tutorial-connect-virtual-machine-classroom-lab.md)
