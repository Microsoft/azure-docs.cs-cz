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
ms.openlocfilehash: e00b6dd5af1cb489aee9e8b4c9f3337eb02e4b14
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878098"
---
# <a name="classroom-lab-setup-guide"></a>Průvodce nastavením učebny v laboratoři

Proces publikování testovacího prostředí pro vaše studenty může trvat až několik hodin, v závislosti na počtu virtuálních počítačů (VM), které se vytvoří ve vaší laboratoři. Vyčkejte alespoň jeden den na nastavení testovacího prostředí, abyste zajistili, že funguje správně, a abyste neměli dostatek času na publikování virtuálních her studentů.

## <a name="understand-the-lab-requirements-of-your-class"></a>Seznamte se s laboratorními požadavky své třídy

Před nastavením nové laboratoře byste měli zvážit následující otázky.

### <a name="what-software-requirements-does-the-class-have"></a>Jaké požadavky na software má třída?

Na základě cílů výuky vaší třídy rozhodněte, který operační systém, aplikace a nástroje je třeba nainstalovat na virtuální počítače testovacího prostředí. Pokud chcete nastavit virtuální aplikace testovacího prostředí, máte tři možnosti:

- **Použití image Azure Marketplace**: Azure Marketplace poskytuje stovky ibi, které můžete použít při vytváření testovacího prostředí. Pro některé třídy jeden z těchto obrázků může již obsahovat vše, co potřebujete pro vaši třídu.

- **Vytvoření nové vlastní image**: Můžete vytvořit vlastní image pomocí image Azure Marketplace jako výchozí bod a přizpůsobit ji instalací dalšího softwaru a provedením změn konfigurace.

- **Použití existujícího vlastního obrázku**: Můžete znovu použít existující vlastní obrázky, které jste dříve vytvořili nebo které byly vytvořeny jinými správci nebo fakultou ve vaší škole. To vyžaduje, aby správci nakonfigurovali sdílenou galerii obrázků, což je úložiště pro ukládání vlastních bitových kopií.

> [!NOTE]
> Vaši správci jsou zodpovědní za povolení ibiah a vlastních ibi azure marketplace, abyste je mohli používat. Koordinujte s oddělením IT a zajistěte, aby byly povoleny obrázky, které potřebujete. Vlastní obrázky, které vytvoříte, jsou automaticky povoleny pro použití v testovacích prostředích, které vlastníte.

### <a name="what-hardware-requirements-does-the-class-have"></a>Jaké hardwarové požadavky má třída?

Existuje celá řada velikostí výpočetních prostředků, ze kterých si můžete vybrat:

- Vnořené velikosti virtualizace, takže můžete studentům udělit přístup k virtuálnímu počítači, který je schopný hostovat více vnořených virtuálních počítačích. Tuto velikost výpočetních prostředků můžete například použít pro síťové kurzy.

- velikosti GPU, aby vaši studenti mohli používat typy aplikací náročných na počítače. Tato volba může být například vhodná pro umělou inteligenci a strojové učení.

Úplný seznam dostupných velikostí výpočetních prostředků najdete v příručce k [velikosti virtuálních mís.](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing)

> [!NOTE]
> V závislosti na oblasti testovacího prostředí se může zobrazit méně výpočetních velikostí, které jsou k dispozici, protože se to liší podle oblasti. Obecně byste měli vybrat nejmenší výpočetní velikost, která je nejblíže vašim potřebám. Se službou Azure Lab Services můžete v případě potřeby později nastavit nové testovací prostředí s jinou výpočetní kapacitou.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Jaké závislosti má třída na externí Azure nebo síťové prostředky?

Pokud vaše virtuální počítače testovacího prostředí potřebují používat externí prostředky, jako je například databáze, sdílení souborů nebo licenční server, koordinujte se správci, abyste zajistili, že vaše testovací prostředí bude mít přístup k těmto prostředkům.

Pro přístup k prostředkům Azure, které *nejsou* zabezpečené virtuální sítí, není nutné hledat další konfiguraci správci. K těmto prostředkům můžete přistupovat prostřednictvím veřejného internetu.

> [!NOTE]
> Měli byste zvážit, zda můžete snížit závislosti vašeho testovacího prostředí na externí prostředky tím, že poskytuje prostředek přímo na virtuálním počítači. Chcete-li například eliminovat potřebu číst data z externí databáze, můžete databázi nainstalovat přímo na virtuální počítač.  

### <a name="how-will-costs-be-controlled"></a>Jak budou náklady kontrolovány?

Lab Services používá model průběžných plateb, což znamená, že platíte jenom za dobu, po kterou je spuštěný virtuální virtuální soud v testovacím prostředí. Chcete-li řídit náklady, máte tři možnosti, které se obvykle používají ve spojení s sebou:

- **Plán**: Plán umožňuje automaticky řídit, kdy jsou virtuální počítače vaše laboratoře spuštěny a vypnuty.
- **Kvóta**: Kvóta určuje počet hodin, po které budou mít studenti přístup k virtuálnímu virtuálnímu účtu mimo naplánované hodiny. Pokud je dosaženo kvóty, když ji student používá, virtuální ho se automaticky vypne. Student není možné restartovat virtuální počítač, pokud se zvýší kvóta.
- **Automatické vypnutí**: Pokud je povoleno, nastavení automatického vypnutí způsobí, že se virtuální počítače systému Windows automaticky vypnou po určité době poté, co se student odpojí od relace RDP (Remote Desktop Protocol). Standardně je toto nastavení zakázáno.  

    > [!NOTE]
    > Toto nastavení v současné době existuje pouze pro systém Windows.

### <a name="how-will-students-save-their-work"></a>Jak studenti zachrání svou práci?

Studentům je přiřazen vlastní virtuální virtuální podnik, který je jim přiřazen po dobu životnosti testovacího prostředí. Mohou si vybrat, zda:

- Uložte přímo do virtuálního virtuálního mísu.
- Uložte ho do externího umístění, třeba Na OneDrivu nebo GitHubu.

OneDrive je možné nakonfigurovat automaticky pro studenty na jejich testovacích virtuálních počítačích.

> [!NOTE]
> Aby bylo zajištěno, že vaši studenti budou mít i nadále přístup k uložené práci mimo laboratoř, a po skončení kurzu doporučujeme studentům uložit svou práci do externího úložiště.

### <a name="how-will-students-connect-to-their-vm"></a>Jak se studenti připojí ke svému virtuálnímu virtuálnímu virtuálnímu zařízení?

Pro RDP na virtuální počítače se systémem Windows doporučujeme studentům používat [klienta Vzdálené plochy společnosti Microsoft](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Klient vzdálené plochy podporuje Macy, Chromebooky a Windows.

Pro virtuální počítače s Linuxem mohou studenti používat SSH nebo RDP. Chcete-li, aby se studenti připojili pomocí protokolu RDP, musíte nainstalovat a nakonfigurovat potřebné balíčky protokolu RDP a GUI.

## <a name="set-up-your-lab"></a>Nastavení testovacího prostředí

Poté, co porozumíte požadavkům na laboratoř vaší třídy, jste připraveni ji nastavit. Podle odkazů v této části se podívejte, jak nastavit testovací prostředí.

1. **Vytvořte testovací prostředí.** Pokyny najdete v kurzu [o vytvoření učebny.](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab)

    > [!NOTE]
    > Pokud vaše třída vyžaduje vnořenou virtualizaci, podívejte se na postup [povolení vnořené virtualizace](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm).

1. **Přizpůsobte si bitové kopie a publikujte virtuální virtuální aplikace testovacího prostředí.** Připojte se ke speciálnímu virtuálnímu počítače nazývanému virtuální počítače šablony. Postup naleznete v následujících průvodcích:
    - [Vytvoření a správa virtuálního počítače šablony](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Využití galerie sdílených imagí](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Pokud používáte Windows, měli byste se také podívat na pokyny při [přípravě virtuálního počítače šablony Windows](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template). Tyto pokyny obsahují postup pro nastavení OneDrivu a Office, které mohou vaši studenti používat.

1. **Správa fondu virtuálních her a kapacity.** Kapacitu virtuálních počítačů můžete snadno vertikálně navýšit nebo snížit podle potřeby vaší třídy. Mějte na paměti, že zvýšení kapacity virtuálních stránek může trvat několik hodin, protože to zahrnuje nastavení nových virtuálních stránek. Postup nastavení [a správy fondu virtuálních her najdete](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)v tématu .

1. **Přidejte a spravujte uživatele testovacího prostředí.** Pokud chcete přidat uživatele do testovacího prostředí, podívejte se na postup v následujících kurzech:
   - [Přidání uživatelů do testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Odesílání pozvánek uživatelům](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Informace o typech účtů, které mohou studenti používat, najdete v [tématu Studentské účty](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts).
  
1. **Nastavte ovládací prvky nákladů.** Chcete-li řídit náklady na testovací prostředí, nastavte plány, kvóty a automatické vypnutí. Projděte si tyto kurzy:

   - [Nastavení plánu](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > V závislosti na typu nainstalovaného operačního systému může spuštění virtuálního počítače trvat několik minut. Chcete-li zajistit, že virtuální počítač testovacího prostředí je připraven k použití během naplánovaných hodin, doporučujeme spustit virtuální počítače 30 minut předem.

   - [Nastavení kvót pro uživatele](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) a [nastavení další kvóty pro konkrétního uživatele](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [Povolení automatického vypnutí při odpojení](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Plány, kvóty a automatické vypnutí se nevztahují na virtuální počítače šablony. V důsledku toho je nutné zajistit, že vypnete virtuální počítače šablony, když se nepoužívá. V opačném případě mu nadále vznikají náklady. Ve výchozím nastavení se také při vytváření testovacího prostředí automaticky spustí virtuální virtuální počítače šablony. Ujistěte se, že okamžitě dokončíte nastavení testovacího prostředí a vypnete virtuální počítače šablony.

1. **Použijte řídicí panel.** Pokyny naleznete [v tématu použití řídicího panelu testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard).

    > [!NOTE]
    > Odhadované náklady zobrazené na řídicím panelu představují maximální náklady, které můžete očekávat pro studenty, kteří jsou v testovacím prostředí. Například vám *not* nebudou studenti účtovat nevyužité hodiny kvót. Odhadované náklady *neodrážejí* žádné poplatky za použití virtuálního počítače šablony nebo sdílené galerie obrázků.

## <a name="next-steps"></a>Další kroky

- [Sledování využití testovacího prostředí v učebně](tutorial-track-usage.md)
  
- [Přístup k testovacímu prostředí v učebně](tutorial-connect-virtual-machine-classroom-lab.md)