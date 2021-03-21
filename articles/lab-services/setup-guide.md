---
title: Průvodce nastavením akcelerovaného testovacího prostředí pro Azure Lab Services
description: Pokud jste autor testovacího prostředí, tento průvodce vám pomůže rychle nastavit účet testovacího prostředí ve škole.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07f0d92ebd926616f1318b430bec2de32f753f7c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95021724"
---
# <a name="lab-setup-guide"></a>Průvodce nastavením testovacího prostředí

V této příručce se dozvíte, jak vytvořit testovací prostředí pro studenty ve škole.

Proces publikování testovacího prostředí pro studenty může trvat až několik hodin. Doba nastavení závisí na počtu virtuálních počítačů, které chcete vytvořit v testovacím prostředí. Počkejte aspoň jeden den, abyste zajistili, že testovací prostředí funguje správně, a umožní vám dostatek času na publikování virtuálních počítačů studentů.

## <a name="understand-the-lab-requirements-of-your-class"></a>Pochopení požadavků testovacího prostředí vaší třídy

Před nastavením nového testovacího prostředí byste měli vzít v úvahu následující otázky.

### <a name="what-software-requirements-does-the-class-have"></a>Jaké softwarové požadavky třída používá?

Pokud se rozhodnete, jaký operační systém, aplikace a nástroje potřebujete nainstalovat na virtuální počítače v testovacím prostředí, přečtěte si záměry studijních kurzů vaší třídy. K nastavení virtuálních počítačů testovacího prostředí máte tři možnosti:

- **Použít Azure Marketplace image**: Azure Marketplace poskytuje stovky imagí, které můžete použít při vytváření testovacího prostředí. U některých tříd může jeden z těchto imagí již obsahovat všechno, co potřebujete pro vaši třídu.

- **Vytvoření nové vlastní image**: jako výchozí bod můžete vytvořit vlastní image pomocí Azure Marketplace image. Pak ho můžete přizpůsobit tak, že nainstalujete další software a provedete změny konfigurace.

- **Použít stávající vlastní image**: můžete znovu použít vlastní image, které jste vytvořili dříve, nebo obrázky, které vytvořili jiní správci nebo vyučující ve vaší škole. Chcete-li použít vlastní image, je nutné, aby správci nastavili galerii sdílených imagí.  Galerie sdílených imagí je úložiště, které se používá k ukládání vlastních imagí.

> [!NOTE]
> Vaši správci zodpovídají za povolení Azure Marketplacech imagí a vlastních imagí, abyste je mohli použít. Zajistěte koordinaci s vaším IT oddělením, abyste měli jistotu, že jsou povolené image, které potřebujete. Vlastní image, které vytvoříte, se automaticky povolí pro použití v rámci testovacích prostředí, která vlastníte.

### <a name="what-hardware-requirements-does-the-class-have"></a>Jaké hardwarové požadavky má třída?

Můžete si vybrat z nejrůznějších výpočetních velikostí:

- **Vnořené velikosti virtualizace**: umožňuje dát studentům přístup k virtuálnímu počítači, který může hostovat více vnořených virtuálních počítačů. Tuto výpočetní velikost můžete například použít pro síťové nebo etické třídy v případě průniku.

- **Velikosti GPU**: umožňuje studentům používat typy aplikací náročných na počítač. Například tato volba se často používá s umělou logikou a strojovým učením.

Pokyny k výběru vhodné velikosti virtuálních počítačů najdete v těchto tématech:
- [Změna velikosti virtuálního počítače](./administrator-guide.md#vm-sizing)
- [Přechod z fyzického testovacího prostředí na Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> Vzhledem k tomu, že se dostupnost výpočetní velikosti liší podle oblasti, může být pro vaše prostředí k dispozici méně velikostí. Obecně byste měli vybrat nejmenší výpočetní velikost, která vyhovuje vašim potřebám. Pomocí Azure Lab Services můžete v případě potřeby nastavit nové testovací prostředí s větší výpočetní kapacitou.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Jaké závislosti má třída na externích zdrojích Azure nebo síťových prostředcích?
Vaše virtuální počítače v testovacím prostředí můžou potřebovat přístup k externím prostředkům, jako je třeba databáze, sdílená složka nebo licenční server.  Pokud chcete, aby virtuální počítače v testovacím prostředí používaly externí prostředky, zajistěte koordinaci se správci IT.

> [!NOTE]
> Měli byste zvážit, jestli můžete snížit závislost testovacího prostředí u externích prostředků tím, že zadáte síťové prostředky přímo na virtuálním počítači. Pokud například chcete eliminovat nutnost čtení dat z externí databáze, můžete nainstalovat databázi přímo na virtuálním počítači.  

### <a name="how-will-you-control-costs"></a>Jak budete řídit náklady?
Testovací služby používají cenový model s průběžnými platbami, což znamená, že platíte jenom za čas, kdy je virtuální počítač testovacího prostředí spuštěný. Chcete-li řídit náklady, použijte některou z následujících možností nebo všechny tyto možnosti:

- **Plán**: pomocí plánů automaticky můžete řídit, kdy se mají virtuální počítače v testovacím prostředí spouštět a vypínat.
- **Kvóta**: pomocí kvót můžete řídit počet hodin, po které mají studenti přístup k virtuálnímu počítači mimo naplánovanou dobu.  Když student používá virtuální počítač a dosáhne kvóty, virtuální počítač se automaticky vypne.  Student nemůže virtuální počítač restartovat, pokud nezvýšíte kvótu.
- **Automatické vypnutí**: Pokud povolíte nastavení automatického vypnutí, virtuální počítače s Windows se automaticky vypnou po odpojení studenta od relace protokol RDP (Remote Desktop Protocol) (RDP). Standardně je toto nastavení zakázáno.

Další informace o řízení nákladů najdete v tématech:
- [Odhadněte náklady](./cost-management-guide.md#estimate-the-lab-costs)
- [Správa nákladů](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>Jak budou studenti ukládat svou práci?
Každému jednomu studentovi se přiřadí virtuální počítač pro celou dobu životnosti testovacího prostředí. Studenti můžou svou práci uložit:

- K virtuálnímu počítači.
- Do externího umístění, jako je OneDrive nebo GitHub. OneDrive je možné nakonfigurovat automaticky pro studenty na svých testovacích virtuálních počítačích.

> [!NOTE]
> Chcete-li zajistit, aby vaši studenti pokračovali v přístupu ke své uložené práci mimo testovací prostředí a po ukončení třídy, doporučujeme, aby svou práci ukládali do externího úložiště.

### <a name="how-will-students-connect-to-their-vms"></a>Jak se studenti připojí ke svým virtuálním počítačům?
Pro připojení RDP k virtuálním počítačům s Windows doporučujeme, aby studenti používali [klienta Vzdálená plocha Microsoft](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Klient vzdálené plochy podporuje zařízení Mac, Chromebook a Windows.

Pro virtuální počítače se systémem Linux můžou studenti použít buď protokol Secure Shell (SSH), nebo protokol RDP. Pokud chcete, aby se studenti připojovali pomocí protokolu RDP, musíte nainstalovat a nakonfigurovat potřebné balíčky RDP a grafického uživatelského rozhraní (GUI).

### <a name="will-students-also-use-microsoft-teams"></a>Budou studenti také používat Microsoft Teams?
Azure Lab Services se integruje s Microsoft teams, aby členové vyučující mohli vytvářet a spravovat své laboratoře v týmech.  Podobně mají studenti přístup k jejich cvičením v týmech.

Další informace najdete v tématu [Azure Lab Services v Microsoft Teams](./lab-services-within-teams-overview.md).

## <a name="set-up-your-lab"></a>Nastavení testovacího prostředí

Až pochopíte požadavky na testovací prostředí vaší třídy, jste připraveni je nastavit. Pokud se chcete dozvědět, jak, postupujte podle odkazů v této části. K dispozici jsou také pokyny pro nastavení Labs v týmech.

1. **Vytvořte testovací prostředí**. Projděte si tyto kurzy:
    - [Vytvoření testovacího prostředí v učebně](./tutorial-setup-classroom-lab.md#create-a-classroom-lab)
    - [Vytvoření testovacího prostředí v týmech](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > Pokud vaše třída vyžaduje vnořenou virtualizaci, přečtěte si téma [Povolení vnořené virtualizace](./how-to-enable-nested-virtualization-template-vm.md).

1. **Přizpůsobení imagí a publikování testovacích virtuálních počítačů**. Pokud se chcete připojit ke speciálnímu virtuálnímu počítači s názvem Template VM, přečtěte si:
    - [Vytvoření a Správa virtuálního počítače šablony](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [Využití galerie sdílených imagí](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > Pokud používáte Windows, přečtěte si také téma [nastavení virtuálního počítače šablony pro Windows](./how-to-prepare-windows-template.md). Tyto pokyny zahrnují kroky pro nastavení OneDrivu a systém Microsoft Office pro studenty.

1. **Spravujte fond virtuálních počítačů a kapacitu**. Kapacitu virtuálního počítače můžete snadno škálovat směrem nahoru nebo dolů podle potřeby vaší třídy. Mějte na paměti, že zvýšení kapacity virtuálních počítačů může trvat několik hodin, protože se nastavují nové virtuální počítače. Viz následující články:
    - [Nastavení a Správa fondu virtuálních počítačů](./how-to-set-virtual-machine-passwords.md)
    - [Správa fondu virtuálních počítačů ve službě Lab Services v týmech](./how-to-manage-vm-pool-within-teams.md)

1. **Přidejte a spravujte uživatele testovacího prostředí**. Pokud chcete přidat uživatele do testovacího prostředí, přečtěte si:
   - [Přidat uživatele do testovacího prostředí](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [Odesílat pozvánky uživatelům](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [Správa seznamů uživatelů testovacích služeb v týmech](./how-to-manage-user-lists-within-teams.md)

    Informace o typech účtů, které studenti můžou používat, najdete v tématu [účty studenta](./how-to-configure-student-usage.md#student-accounts).
  
1. **Nastavte řízení nákladů**. Pokud chcete nastavit plán, vytvořit kvóty a povolit automatické vypnutí, přečtěte si následující kurzy:

   - [Nastavit plán](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > V závislosti na operačním systému, který jste nainstalovali, může spuštění virtuálního počítače trvat několik minut. Aby se zajistilo, že testovací virtuální počítač je připravený k použití během naplánovaných hodin, doporučujeme, abyste ho spustili předem za 30 minut.

   - [Nastavte kvóty pro uživatele](./how-to-configure-student-usage.md#set-quotas-for-users) a [nastavte další kvóty pro konkrétní uživatele](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users) .
  
   - [Povolení automatického vypnutí při odpojení](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > Plány a kvóty se nevztahují na virtuální počítač šablony, ale použijí se nastavení automatického vypnutí. 
        > 
        > Při vytváření testovacího prostředí se vytvoří virtuální počítač šablony, ale ne spuštěný. Můžete spustit šablonu virtuálního počítače, připojit se k němu, nainstalovat libovolný nezbytný software pro testovací prostředí a pak ho publikovat. Když publikujete virtuální počítač šablony, automaticky se vypíná za vás, pokud jste to neudělali ručně. 
        > 
        > Virtuální počítače s šablonou účtují *náklady* , pokud jsou spuštěné, takže se ujistěte, že je virtuální počítač šablony vypnutý, když ho nepotřebujete používat.

    - [Vytváření a Správa plánů testovacích služeb v týmech](./how-to-create-schedules-within-teams.md) 

1. **Použijte řídicí panel**. Pokyny najdete v tématu [Použití řídicího panelu prostředí učebny](./use-dashboard.md).

    > [!NOTE]
    > Odhadované náklady zobrazené na řídicím panelu jsou maximální náklady, které můžete očekávat pro použití v testovacím prostředí studenta. Nebudete se například  účtovat za nevyužité pracovní doby vaší studenty. Odhadované náklady *neodráží žádné* poplatky za použití virtuálního počítače šablony, Galerie sdílených imagí nebo když tvůrce testovacího prostředí spustí uživatelský počítač.

## <a name="next-steps"></a>Další kroky

V rámci správy cvičení si přečtěte následující články:
- [Sledování využití testovacího prostředí učebny](tutorial-track-usage.md)  
- [Přístup k testovacímu prostředí v učebně](tutorial-connect-virtual-machine-classroom-lab.md)
