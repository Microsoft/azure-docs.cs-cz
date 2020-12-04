---
title: Použití Azure Lab Services pro Hackathon
description: Tento článek popisuje, jak používat Azure Lab Services pro vytváření testovacích prostředí, která můžete použít ke spuštění hackathony.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a0917ede4502dcbb59d1a30b7985b06c06975599
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602576"
---
# <a name="use-azure-lab-services-for-your-next-hackathon"></a>Použít Azure Lab Services pro další Hackathon
Azure Lab Services je navržená tak, aby byla nenáročná a snadno použitelná, takže můžete rychle vymezit nové prostředí virtuálních počítačů (VM) pro vaši Hackathon.  Pomocí následujícího kontrolního seznamu zajistěte, aby váš Hackathon byl co nejrychleji. Tento kontrolní seznam by měl dokončit vaše IT oddělení nebo vyučující, kteří zodpovídají za vytváření a správu testovacího prostředí Hackathon. 

Pokud chcete používat služby testovacího prostředí pro Hackathon, ujistěte se, že je před začátkem Hackathon vytvořeno jak účet testovacího prostředí, tak i testovací prostředí alespoň několik dní. Postupujte také podle pokynů níže:

## <a name="guidance"></a>Pokyny

- **Vytvořte testovací prostředí v oblasti nebo umístění, které je nejblíže účastníkům**. 

    Pokud chcete snížit latenci, vytvořte testovací prostředí v oblasti, která je nejblíže vašim účastníkům Hackathon.  Pokud se účastníci nacházejí po celém světě, je třeba použít k vytvoření testovacího prostředí, které je centrálně umístěné, své nejlepší rozhodnutí.  Nebo můžete rozdělit Hackathon na použití více cvičení na základě umístění, kde se účastníci nacházejí.
- **Vyberte si velikost pro výpočetní výkon, která nejlépe vyhovuje potřebám použití**.

    Obecně platí, že čím větší je výpočetní velikost, tím rychleji bude virtuální počítač fungovat. Chcete-li však omezit náklady, bude nutné vybrat odpovídající výpočetní velikost na základě potřeb vašich účastníků. Podrobnosti o dostupných velikostech výpočtů najdete v tématu informace o nastavení [velikosti virtuálního počítače v příručce pro správce](administrator-guide.md#vm-sizing) .
- **Nakonfigurujte RDP\SSH pro připojení ke vzdálené ploše pro virtuální počítače se systémem Linux**.

    Pokud vaše Hackathon používá virtuální počítače se systémem Linux, ujistěte se, že je povolená Vzdálená plocha, aby mohli vaši účastníci připojit ke svým virtuálním počítačům buď pomocí protokolu RDP (Remote Desktop Protocol), nebo SSH (Secure Shell). Tento krok se vyžaduje jenom pro virtuální počítače se systémem Linux a při vytváření testovacího prostředí musí být povolený. Pro protokol RDP taky možná budete muset před publikováním nainstalovat a nakonfigurovat server RDP a balíčky grafického uživatelského rozhraní na virtuálním počítači šablony.  Další informace najdete v tématu Návod k [Povolení vzdálené plochy pro Linux](how-to-enable-remote-desktop-linux.md).

- **Instalace a zastavení aktualizací Windows**. 

    Pokud používáte image Windows, doporučujeme nainstalovat nejnovější aktualizace Windows na [virtuálním počítači šablony](how-to-create-manage-template.md) testovacího prostředí před tím, než ho publikujete pro vytváření virtuálních počítačů Labs. Je z bezpečnostních důvodů a brání účastníkům v jejich přerušení během Hackathon k instalaci aktualizací, což může také způsobit restartování svých virtuálních počítačů. Můžete také zvážit vypnutí aktualizací Windows, aby nedocházelo k budoucímu přerušení. Přečtěte si návod k [instalaci a konfiguraci aktualizací Windows](how-to-prepare-windows-template.md#install-and-configure-updates).
- **Rozhodněte se, jak studenti budou zálohovat svou práci**. 

    Studentům jsou každý přiřazený virtuální počítač pro celou dobu životnosti Hackathon. Můžou ukládat svou práci přímo na počítač, ale doporučuje se, aby studenti zálohovali svou práci, aby k nim měli přístup i po skončení Hackathon. Například by se měly ukládat do externího umístění, jako je OneDrive, GitHub a tak dále. Pokud chcete použít OneDrive, můžete ho nakonfigurovat pro studenty na svých virtuálních počítačích testovacího prostředí automaticky. Přečtěte si [návod, jak nainstalovat a nakonfigurovat OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive).
- **Nastavte kapacitu virtuálního počítače podle počtu účastníků**. 

    Zajistěte, aby byla kapacita virtuálního počítače vašeho testovacího prostředí nastavená na základě počtu účastníků, které očekáváte v Hackathon. Když publikujete virtuální počítač šablony, může trvat několik hodin, než se vytvoří všechny počítače v testovacím prostředí. To je důvod, proč doporučujeme, abyste předem měli na začátku Hackathon. Další informace najdete v tématu [Průvodce aktualizací kapacity testovacího prostředí](how-to-set-virtual-machine-passwords.md#update-the-lab-capacity).

- **Rozhodněte, jestli se má omezit přístup k testovacímu prostředí**. 

    Při přidávání uživatelů do testovacího prostředí je k dispozici možnost omezit přístup, která je ve výchozím nastavení povolená. Tato funkce vyžaduje, abyste přidali všechny e-maily účastníků Hackathon do seznamu předtím, než se budou moct zaregistrovat a přistupovat k testovacímu prostředí pomocí odkazu registrace. Pokud máte Hackathon, kde nevíte, kdo budou účastníci před událostí, můžete zakázat možnost omezit přístup, která umožňuje komukoli zaregistrovat se do testovacího prostředí pomocí odkazu na registraci. Další informace najdete v [příručce Průvodce přidáváním uživatelů](how-to-configure-student-usage.md).

- **Ověřte nastavení plánu, kvót a automatické vypnutí**. 

    Testovací služby poskytují několik řízení nákladů na omezení využití virtuálních počítačů. Pokud jsou ale tato nastavení chybně nakonfigurované, můžou způsobit neočekávané vypnutí virtuálních počítačů v testovacím prostředí. Pokud chcete zajistit, aby byla tato nastavení pro vaše Hackathon správně nakonfigurovaná, ověřte následující nastavení:

    **Plán**: [plán](how-to-create-schedules.md) vám umožní automaticky řídit, kdy se počítače v laboratořích spouštějí a vypnou. Ve výchozím nastavení není při vytváření nového testovacího prostředí nakonfigurován žádný plán. Měli byste se ale ujistit, že je plán testovacího prostředí nastavený podle toho, co dává smysl pro vaše Hackathon.  Příklad: Pokud vaše Hackathon začíná v sobotu v 8:00. a končí v neděli v 5:00 odp. můžete vytvořit plán, který automaticky spustí počítač 7:30 v sobotu (přibližně 30 minut před začátkem Hackathon) a vypne se v 5:00 odpoledne v neděli. Místo toho se také můžete rozhodnout, že nebudete vůbec používat plán.

    **Kvóta**: [kvóta](how-to-configure-student-usage.md#set-quotas-for-users) řídí počet hodin, po které budou mít účastníci přístup k virtuálnímu počítači mimo naplánovanou dobu. Pokud je dosažena kvóta, když ji účastník používá, počítač se automaticky vypne a účastník ho nebude moct restartovat, pokud se kvóta nezvýší. Ve výchozím nastavení platí, že při vytváření testovacího prostředí je kvóta nastavená na 10 hodin. Znovu byste měli nastavit kvótu tak, aby umožňovala dostatek času pro Hackathon, což je obzvláště důležité, pokud jste plán nevytvořili.

    Automatické **vypnutí**: Pokud je tato možnost povolená, nastavení automatického [vypnutí](how-to-enable-shutdown-disconnect.md) způsobí, že se virtuální počítače s Windows po uplynutí určité doby automaticky vypnou, jakmile se student odpojí od své relace RDP. Standardně je toto nastavení zakázáno.

- **Nakonfigurujte nastavení brány firewall tak, aby povolovala připojení k testovacím virtuálním** počítačům. 

    Ujistěte se, že nastavení brány firewall ve škole nebo ve vaší organizaci umožňuje připojení k virtuálním počítačům testovacího prostředí pomocí RDP\SSH.. Další informace najdete v tématu [Průvodce konfigurací nastavení brány firewall vaší sítě](how-to-configure-firewall-settings.md).

- **Nainstalujte klienta RDP\SSH na tablety, počítače Mac, počítače a tak dále**.

    Účastníci Hackathon musí mít nainstalovaného klienta RDP nebo SSH na svých tabletech nebo na laptopech, které budou používat pro připojení k testovacím virtuálním počítačům. Můžete si vybrat z různých klientů RDP nebo SSH, například:

    - Aplikace **připojení ke vzdálené ploše** Microsoftu pro připojení RDP. Aplikace Připojení ke vzdálené ploše je podporovaná na různých druzích platforem, včetně Chromebooks a [Mac](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162).
    - Výstup [pro použití SSH pro připojení](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162) k virtuálnímu počítači se systémem Linux.
- **Ověřte virtuální počítače testovacího prostředí**. 

    Po publikování testovacích virtuálních počítačů byste měli ověřit, jestli jsou správně nakonfigurované. Toto ověření musíte provést jenom pro jedno z testovacích virtuálních počítačů daného účastníka:

    1. Připojte se pomocí protokolu RDP and\or SSH.
    2. Otevřete všechny další aplikace a nástroje, které jste nainstalovali k přizpůsobení základní image virtuálního počítače.
    3. Projděte si několik základních scénářů, které jsou zástupci činností, které by účastníci provedli, aby se zajistil výkon virtuálního počítače na základě vybrané výpočetní velikosti.

## <a name="on-the-day-of-hackathon"></a>Den Hackathon
Tato část popisuje kroky pro dokončení dne vašeho hackathonu.

1. **Spusťte testovací virtuální počítače**.

    V závislosti na operačním systému může spuštění testovacího počítače trvat až 30 minut. V důsledku toho je důležité zahájit počítače před spuštěním Hackathon, aby se účastníci nemuseli čekat. Pokud používáte plán, zajistěte, aby se virtuální počítače automaticky spustily alespoň 30 minut.
2. **Pozvěte studenty k registraci a přístupu ke svému virtuálnímu počítači testovacího prostředí**. 

    Poskytněte svým účastníkům následující informace, aby mohli účastníci získat přístup k testovacím virtuálním počítačům. 

    - Odkaz na registraci testovacího prostředí. 
    - Přihlašovací údaje, které by měly být použity pro připojení k počítači. Tento krok se vztahuje pouze v případě, že testovací prostředí používá bitovou kopii systému Windows a Vy jste nakonfigurovali všechny virtuální počítače, aby používaly stejné heslo.
    - Pokyny, jak účastníci SSH and\or RDP ke svým počítačům.

        Další informace najdete v tématu Návod, [Jak odesílat pozvánky uživatelům](how-to-configure-student-usage.md?branch=master#send-invitations-to-users) a [připojovat se k virtuálním počítačům se systémem Linux](how-to-use-remote-desktop-linux-student.md?branch=master). 

## <a name="next-steps"></a>Další kroky
Začněte vytvořením účtu testovacího prostředí v cvičení podle pokynů v článku [kurz: nastavení účtu testovacího prostředí pomocí Azure Lab Services](tutorial-setup-lab-account.md).
