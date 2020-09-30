---
title: Jak nastavit server SMT pro SAP HANA v Azure (velké instance) | Microsoft Docs
description: Jak nastavit server SMT pro SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c186b73cb00d03b731cd015b3ee06bf8f2233fa4
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541148"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Nastavení serveru SMT pro SUSE Linux
Velké instance SAP HANA nemají přímé připojení k Internetu. Nejedná se o přímočarý proces k registraci takové jednotky s poskytovatelem operačního systému a ke stažení a instalaci aktualizací. Řešením pro SUSE Linux je nastavení serveru SMT na virtuálním počítači Azure. Virtuální počítač hostte ve virtuální síti Azure, která je připojená k velké instanci HANA. U takového serveru SMT by mohla jednotka velké instance HANA registrovat a stahovat aktualizace. 

Další dokumentaci k SUSE najdete v tématu [Nástroj pro správu předplatného pro SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Požadavky pro instalaci serveru SMT, který plní úlohu pro velké instance HANA, jsou tyto:

- Virtuální síť Azure, která je připojená k okruhu ExpressRoute velkých instancí HANA.
- Účet SUSE, který je spojený s organizací. Organizace by měla mít platné předplatné SUSE.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Instalace SMT serveru na virtuální počítač Azure

Nejdřív se přihlaste do [centra pro zákazníky v SUSE](https://scc.suse.com/).

Přejít na **Organization**  >  **přihlašovací údaje organizace**organizace. V této části byste měli najít přihlašovací údaje, které jsou nutné k nastavení serveru SMT.

Pak ve virtuální síti Azure nainstalujte virtuální počítač s SUSE Linux. Pokud chcete nasadit virtuální počítač, vezměte image z Galerie SLES 12 SP2 do Azure (vyberte BYOS SUSE image). V procesu nasazení nedefinujte název DNS a nepoužívejte statické IP adresy.

![Snímek obrazovky s nasazením virtuálního počítače pro server SMT](./media/hana-installation/image3_vm_deployment.png)

Nasazený virtuální počítač je menší a získal interní IP adresu ve virtuální síti Azure 10.34.1.4. Název virtuálního počítače je *smtserver*. Po dokončení instalace se zaškrtne možnost připojení k jednotce nebo jednotkám velké instance HANA. V závislosti na tom, jak organizujete překlad IP adres, možná budete muset nakonfigurovat rozlišení jednotek velkých instancí HANA v hostitelích a na virtuálních počítačích Azure. 

Přidejte disk do virtuálního počítače. Tento disk použijete k uložení aktualizací a samotný spouštěcí disk může být příliš malý. V tomto případě se disk připojil k/SRV/www/htdocs, jak je znázorněno na následujícím snímku obrazovky. By měl stačit disk 100-GB.

![Snímek obrazovky s přidaným diskem se zobrazí v okně výstupu.](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Přihlaste se k jednotce nebo jednotkám velké instance HANA, udržujte/etc/hosts a ověřte, jestli se můžete dostat k virtuálnímu počítači Azure, který by měl spustit server SMT přes síť.

Po této kontrole se přihlaste k virtuálnímu počítači Azure, který by měl spustit server SMT. Pokud k přihlášení k virtuálnímu počítači používáte výstup, spusťte v okně bash následující posloupnost příkazů:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Pro aktivaci nastavení restartujte bash. Pak spusťte YAST.

Připojte svůj virtuální počítač (smtserver) k webu SUSE.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Jakmile je virtuální počítač připojený k webu SUSE, nainstalujte balíčky SMT. K instalaci balíčků SMT použijte následující příkaz k výstupu.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


K instalaci balíčků SMT můžete použít také nástroj YAST. V YAST pokračujte na **Údržba softwaru**a vyhledejte SMT. Vyberte **SMT**, které automaticky přepne na YaST2-SMT.

![Snímek obrazovky SMT v YAST](./media/hana-installation/image5_smt_in_yast.PNG)


Přijměte výběr pro instalaci na smtserver. Po dokončení instalace přejdete do konfigurace serveru SMT. Zadejte přihlašovací údaje organizace z centra zákazníků SUSE, které jste získali dříve. Jako adresu URL serveru SMT zadejte taky název hostitele virtuálního počítače Azure. V této ukázce je to https: \/ /smtserver.

![Snímek obrazovky s konfigurací serveru SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Nyní otestujte, jestli připojení k centru zákazníků SUSE funguje. Jak vidíte na následujícím snímku obrazovky, v tomto ukázkovém případě to fungovalo.

![Snímek obrazovky s testováním připojení k SUSE Customer Center](./media/hana-installation/image7_test_connect.png)

Po spuštění instalačního programu SMT zadejte heslo databáze. Vzhledem k tomu, že se jedná o novou instalaci, měli byste toto heslo definovat, jak je znázorněno na následujícím snímku obrazovky.

![Snímek obrazovky definující heslo pro databázi](./media/hana-installation/image8_define_db_passwd.PNG)

Dalším krokem je vytvoření certifikátu.

![Snímek obrazovky s vytvořením certifikátu pro server SMT](./media/hana-installation/image9_certificate_creation.PNG)

Na konci konfigurace může trvat několik minut, než se spustí ověření synchronizace. Po instalaci a konfiguraci serveru SMT byste měli najít úložiště adresáře pod přípojným bodem/SRV/www/htdocs/. Existují také některé podadresáře v úložišti. 

Pomocí těchto příkazů restartujte server SMT a související služby.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Stáhnout balíčky na server SMT

Po restartování všech služeb vyberte příslušné balíčky v SMT Management pomocí YAST. Výběr balíčku závisí na bitové kopii operačního systému serveru rozsáhlých instancí HANA. Výběr balíčku není závislý na verzi SLES nebo verzi virtuálního počítače, na kterém běží SMT Server. Na následujícím snímku obrazovky vidíte příklad obrazovky pro výběr.

![Snímek obrazovky s výběrem balíčků](./media/hana-installation/image10_select_packages.PNG)

Dále spusťte počáteční kopii vybraných balíčků na serveru SMT, který jste nastavili. Tato kopie se aktivuje v prostředí pomocí příkazu SMT-Mirror.

![Snímek obrazovky s stažením balíčků na server SMT](./media/hana-installation/image11_download_packages.PNG)

Balíčky by se měly zkopírovat do adresářů vytvořených pod přípojným bodem/SRV/www/htdocs. Tento proces může trvat hodinu nebo déle, a to v závislosti na tom, kolik balíčků vyberete. Po dokončení tohoto procesu se přesuňte k instalaci klienta SMT. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Nastavení klienta SMT na jednotkách velkých instancí HANA

Klient nebo klienti v tomto případě jsou jednotkami velkých instancí HANA. Instalační program serveru SMT zkopíroval skript clientSetup4SMT.sh do virtuálního počítače Azure. Zkopírujte tento skript do jednotky velkých instancí HANA, kterou chcete připojit k serveru SMT. Spusťte skript s možností-h a jako parametr zadejte název serveru SMT. V tomto příkladu je název *smtserver*.

![Snímek obrazovky konfigurace klienta SMT](./media/hana-installation/image12_configure_client.PNG)

Je možné, že zatížení certifikátu ze serveru klienta proběhne úspěšně, ale registrace se nezdaří, jak je znázorněno na následujícím snímku obrazovky.

![Snímek obrazovky s chybou registrace klienta](./media/hana-installation/image13_registration_failed.PNG)

Pokud se registrace nepovede, přečtěte si [dokument podpory SUSE](https://www.suse.com/de-de/support/kb/doc/?id=7006024)a spusťte kroky popsané tady.

> [!IMPORTANT] 
> Jako název serveru zadejte název virtuálního počítače (v tomto případě *smtserver*) bez plně kvalifikovaného názvu domény. 

Po spuštění těchto kroků spusťte následující příkaz na jednotce velkých instancí HANA:

```
SUSEConnect –cleanup
```

> [!Note] 
> Počkejte několik minut od tohoto kroku. Pokud clientSetup4SMT.sh spustíte hned, může se zobrazit chyba.

Pokud narazíte na problém, který je třeba opravit na základě kroků v článku SUSE, restartujte clientSetup4SMT.sh na jednotce velké instance HANA. Teď by se měl úspěšně dokončit.

![Snímek obrazovky s úspěšným registrací klienta](./media/hana-installation/image14_finish_client_config.PNG)

Nakonfigurovali jste klienta SMT jednotky velkých instancí HANA pro připojení k serveru SMT, který jste nainstalovali na virtuálním počítači Azure. Nyní můžete provést příkaz "zypperu nahoru" nebo "zypperu in" a nainstalovat aktualizace operačního systému do rozsáhlých instancí HANA nebo nainstalovat další balíčky. Aktualizace, které jste stáhli do serveru SMT, můžete získat jenom.

## <a name="next-steps"></a>Další kroky
- [Instalace Hana na HLI](hana-example-installation.md)











