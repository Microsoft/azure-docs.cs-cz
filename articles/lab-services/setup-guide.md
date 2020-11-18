---
title: Průvodce nastavením akcelerovaného testovacího prostředí pro Azure Lab Services
description: Tato příručka pomáhá tvůrcům testovacího prostředí rychle nastavit účet testovacího prostředí pro použití v rámci své školy.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8ef168aefb69df32f57b623bb488adbb97cbd411
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659671"
---
# <a name="lab-setup-guide"></a>Průvodce nastavením testovacího prostředí

Proces publikování testovacího prostředí pro studenty může trvat až několik hodin.  Doba závisí na počtu virtuálních počítačů, které budou vytvořeny v testovacím prostředí. Umožněte aspoň jeden den v nastavení testovacího prostředí, abyste měli jistotu, že funguje správně, a umožníte dostatek času na publikování virtuálních počítačů studentů.

## <a name="understand-the-lab-requirements-of-your-class"></a>Pochopení požadavků testovacího prostředí vaší třídy

Před nastavením nového testovacího prostředí byste měli vzít v úvahu následující otázky.

### <a name="what-software-requirements-does-the-class-have"></a>Jaké softwarové požadavky třída používá?

Na základě studijních cílů vaší třídy rozhodněte, které operační systémy, aplikace a nástroje je potřeba nainstalovat na virtuální počítače testovacího prostředí. K nastavení virtuálních počítačů testovacího prostředí máte tři možnosti:

- **Použít Azure Marketplace image**: Azure Marketplace poskytuje stovky imagí, které můžete použít při vytváření testovacího prostředí. U některých tříd může jeden z těchto imagí již obsahovat všechno, co potřebujete pro vaši třídu.

- **Vytvoření nové vlastní image**: můžete vytvořit vlastní image pomocí Azure Marketplace Image jako výchozí bod a přizpůsobit ji tím, že nainstalujete další software a provedete změny konfigurace.

- **Použít stávající vlastní image**: můžete znovu použít stávající vlastní image, které jste vytvořili dříve, nebo které vytvořili jiní správci nebo vyučující ve vaší škole. Chcete-li použít vlastní image, je nutné, aby správci nastavili galerii sdílených imagí.  Galerie sdílených imagí je úložiště, které se používá k ukládání vlastních imagí.

> [!NOTE]
> Vaši správci zodpovídají za povolení Azure Marketplacech imagí a vlastních imagí, abyste je mohli použít. Zajistěte koordinaci s vaším IT oddělením, abyste měli jistotu, že jsou povolené potřebné obrázky. Vlastní image, které vytvoříte, se automaticky povolí pro použití v rámci testovacích prostředí, která vlastníte.

### <a name="what-hardware-requirements-does-the-class-have"></a>Jaké hardwarové požadavky má třída?

Existují různé výpočetní velikosti, ze kterých si můžete vybrat:

- Vnořené velikosti virtualizace, abyste mohli dát studentům přístup k virtuálnímu počítači, který může hostovat více vnořených virtuálních počítačů. Tuto výpočetní velikost můžete například použít pro síťové nebo etické třídy v případě průniku.

- Velikosti GPU, aby mohli studenti používat typy aplikací náročných na počítač. Například tato volba se často používá s umělou logikou a strojovým učením.

Pokyny k výběru vhodné velikosti virtuálního počítače najdete v následujících článcích:
- [Změna velikosti virtuálního počítače](./administrator-guide.md#vm-sizing)
- [Přechod z fyzického testovacího prostředí na Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> V závislosti na oblasti testovacího prostředí se může zobrazit méně dostupných velikostí výpočetních prostředků, protože se liší podle oblasti. Obecně byste měli vybrat nejmenší výpočetní velikost, která je nejblíže vašim potřebám. Pomocí Azure Lab Services můžete v případě potřeby nastavit nové testovací prostředí s jinou výpočetní kapacitou.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Jaké závislosti má třída na externích zdrojích Azure nebo síťových prostředcích?
Vaše virtuální počítače v testovacím prostředí můžou potřebovat přístup k externím prostředkům, jako je například přístup k databázi, sdílené složce nebo licenčnímu serveru.  Pokud chcete, aby virtuální počítače v testovacím prostředí používaly externí prostředky, zajistěte koordinaci se správci IT.

> [!NOTE]
> Měli byste zvážit, jestli můžete snížit závislosti testovacího prostředí na externí prostředky tím, že poskytnete prostředek přímo na virtuálním počítači. Pokud například chcete eliminovat nutnost čtení dat z externí databáze, můžete nainstalovat databázi přímo na virtuálním počítači.  

### <a name="how-will-costs-be-controlled"></a>Jak se budou řídit náklady?
Testovací služby používají cenový model s průběžnými platbami, což znamená, že platíte jenom za čas, kdy je virtuální počítač testovacího prostředí spuštěný. Chcete-li řídit náklady, máte tři možnosti, které se obvykle používají společně:

- **Plán**: plán vám umožní automaticky řídit, kdy se virtuální počítače Labs spustí a ukončí.
- **Kvóta**: kvóta řídí počet hodin, po které studenti budou mít přístup k virtuálnímu počítači mimo plánované hodiny.  Když student používá svůj virtuální počítač a dosáhli jste jeho kvóty, virtuální počítač se automaticky vypne.  Student nemůže virtuální počítač restartovat, pokud se kvóta nezvýší.
- Automatické **vypnutí**: Pokud je povoleno, nastavení automatického vypnutí způsobí, že se virtuální počítače s Windows automaticky vypnou po odpojení studenta od relace protokol RDP (Remote Desktop Protocol) (RDP). Standardně je toto nastavení zakázáno.

Další informace najdete v následujících článcích:
- [Odhadněte náklady](./cost-management-guide.md#estimate-the-lab-costs)
- [Správa nákladů](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>Jak budou studenti ukládat svou práci?
Studentům se přiřadí vlastní virtuální počítač, který se jim přiřadí za dobu života testovacího prostředí. Můžou zvolit tyto možnosti:

- Uložte se přímo do virtuálního počítače.
- Uložte si do externího umístění, jako je OneDrive nebo GitHub.

OneDrive je možné nakonfigurovat automaticky pro studenty na svých testovacích virtuálních počítačích.

> [!NOTE]
> Abychom zajistili, že studenti budou pokračovat v přístupu ke své uložené práci mimo testovací prostředí a po ukončení třídy, doporučujeme, aby studenti svou práci ukládali do externího úložiště.

### <a name="how-will-students-connect-to-their-vm"></a>Jak se studenti připojí ke svému virtuálnímu počítači?
U protokolu RDP na virtuální počítače s Windows doporučujeme, aby studenti používali [klienta Vzdálená plocha Microsoft](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Klient vzdálené plochy podporuje Mac, Chromebooks a Windows.

Pro virtuální počítače se systémem Linux můžou studenti použít buď SSH, nebo RDP. Pokud chcete, aby se studenti připojovali pomocí protokolu RDP, musíte nainstalovat a nakonfigurovat potřebné balíčky RDP a GUI.

### <a name="will-students-also-be-using-microsoft-teams"></a>Budou studenti také používat Microsoft Teams?
Azure Lab Services se integruje s Microsoft teams, aby vyučující mohli vytvářet a spravovat své laboratoře v rámci týmů.  Podobně mají studenti přístup k testovacímu prostředí v rámci týmů.

Další informace najdete v následujícím článku:
- [Azure Lab Services v rámci Microsoft Teams](./lab-services-within-teams-overview.md)

## <a name="set-up-your-lab"></a>Nastavení testovacího prostředí

Až pochopíte požadavky na testovací prostředí vaší třídy, jste připraveni je nastavit. Pomocí odkazů v této části zjistíte, jak nastavit testovací prostředí.  Všimněte si, že jsou k dispozici různé kroky v závislosti na tom, zda používáte testovací prostředí v rámci týmů.

1. **Vytvořte testovací prostředí.** Přečtěte si kurzy k vytvoření testovacího prostředí:
    - Pro pokyny [vytvořte prostředí učebny](./tutorial-setup-classroom-lab.md#create-a-classroom-lab) .
    - [Vytvoření testovacího prostředí z Teams](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > Pokud vaše třída vyžaduje vnořenou virtualizaci, přečtěte si postup [Povolení vnořené virtualizace](./how-to-enable-nested-virtualization-template-vm.md).

1. **Přizpůsobení imagí a publikování testovacích virtuálních počítačů.** Připojte se ke speciálnímu virtuálnímu počítači, který se nazývá virtuální počítač šablony. Postup najdete v následujících příručkách:
    - [Vytvoření a Správa virtuálního počítače šablony](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [Využití galerie sdílených imagí](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > Pokud používáte systém Windows, měli byste se také podívat na pokyny v tématu [Příprava virtuálního počítače šablony pro Windows](./how-to-prepare-windows-template.md). Tyto pokyny obsahují kroky pro nastavení OneDrivu a Office pro vaše studenty, kteří budou používat.

1. **Spravujte fond virtuálních počítačů a kapacitu.** Kapacitu virtuálního počítače můžete snadno škálovat směrem nahoru nebo dolů podle potřeby vaší třídy. Mějte na paměti, že zvýšení kapacity virtuálních počítačů může trvat několik hodin, protože se nastavují nové virtuální počítače. Projděte si postup v následujících článcích:
    - [Nastavení a Správa fondu virtuálních počítačů](./how-to-set-virtual-machine-passwords.md)
    - [Správa fondu virtuálních počítačů ve službě Lab Services z týmů](./how-to-manage-vm-pool-within-teams.md)

1. **Přidejte a spravujte uživatele testovacího prostředí.** Pokud chcete přidat uživatele do testovacího prostředí, přečtěte si postup v následujících kurzech:
   - [Přidat uživatele do testovacího prostředí](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [Odesílat pozvánky uživatelům](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [Správa seznamů uživatelů testovacích služeb z týmů](./how-to-manage-user-lists-within-teams.md)

    Informace o typech účtů, které studenti můžou používat, najdete v tématu [účty studenta](./how-to-configure-student-usage.md#student-accounts).
  
1. **Nastavte řízení nákladů.** Chcete-li řídit náklady na testovací prostředí, nastavte plány, kvóty a automatické vypnutí. Projděte si tyto kurzy:

   - [Nastavit plán](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > V závislosti na typu operačního systému, který jste nainstalovali, může spuštění virtuálního počítače trvat několik minut. Aby se zajistilo, že testovací virtuální počítač je připravený k použití během naplánovaných hodin, doporučujeme začít virtuální počítače 30 minut předem.

   - [Nastavte kvóty pro uživatele](./how-to-configure-student-usage.md#set-quotas-for-users) a [nastavte další kvótu pro konkrétního uživatele](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users) .
  
   - [Povolení automatického vypnutí při odpojení](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > Plány a kvóty se nevztahují na virtuální počítač šablony, ale nastavení automatického vypnutí se použije. 
        > 
        > Při vytváření testovacího prostředí se vytvoří virtuální počítač šablony, který ale není spuštěný. Můžete ji spustit, připojit k ní a nainstalovat libovolný nezbytný software pro testovací prostředí a pak ho publikovat. Když publikujete virtuální počítač šablony, automaticky se vypíná za vás, pokud jste to ještě neudělali. 
        > 
        > Virtuální počítače šablony účtují **náklady** při jejich spuštění, takže se ujistěte, že je virtuální počítač šablony vypnutý, když ho nepotřebujete používat.

    - [Vytváření a Správa plánů testovacích služeb v rámci týmů](./how-to-create-schedules-within-teams.md) 

1. **Použijte řídicí panel.** Pokyny najdete v tématu [Použití řídicího panelu testovacího prostředí](./use-dashboard.md).

    > [!NOTE]
    > Odhadované náklady zobrazené na řídicím panelu jsou maximální náklady, které můžete očekávat od využívání testovacího prostředí pro studenty. Nebudete se například *not* účtovat za nevyužité pracovní doby vaší studenty. Odhadované náklady *neodráží žádné* poplatky za použití virtuálního počítače šablony, Galerie sdílených imagí nebo když tvůrce testovacího prostředí spustí uživatelský počítač.

## <a name="next-steps"></a>Další kroky

- [Sledování využití testovacího prostředí v učebně](tutorial-track-usage.md)
  
- [Přístup k testovacímu prostředí v učebně](tutorial-connect-virtual-machine-classroom-lab.md)