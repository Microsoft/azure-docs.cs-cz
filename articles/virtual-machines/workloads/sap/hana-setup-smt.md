---
title: Jak nastavit SMT server pro SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Jak nastavit SMT server pro SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d9fe644b7cc7d1a13cb9ed2f7016f25b3e346dfb
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233170"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Nastavení serveru SMT operačním systémem SUSE Linux
Velké instance SAP HANA nemají přímé připojení k Internetu. Není jednoduchý proces registrace jako jednotek s poskytovateli operačního systému a ke stažení a použití aktualizací. Řešení SUSE Linux je nastavení serveru SMT ve virtuálním počítači Azure. Hostování tohoto virtuálního počítače ve službě Azure virtual network, která je připojena k velká Instance HANA. Pomocí těchto SMT serveru může jednotka velká Instance HANA zaregistrovat a stáhnout aktualizace. 

Další dokumentaci k systému SUSE, najdete jejich [nástroj pro správu předplatného pro SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Předpoklady pro instalaci serveru SMT, který splňuje požadavky úlohy pro velké instance HANA je:

- Virtuální síť Azure, která je připojena k okruhu ExpressRoute velké Instance HANA.
- SUSE účet, který je spojen s organizací. Organizace by měly mít platné předplatné, SUSE.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>SMT server nainstalovat na virtuální počítač Azure

Nejprve se přihlaste k [centrum zákazníků SUSE](https://scc.suse.com/).

Přejděte na **organizace** > **přihlašovacími údaji organizace**. V této části měli byste najít přihlašovací údaje, které jsou potřebné k nastavení SMT serveru.

Nainstalujte virtuálního počítače s operačním systémem SUSE Linux ve službě Azure virtual network. Pokud chcete nasadit virtuální počítač, proveďte SLES 12 SP2 image z Galerie Azure (vyberte image operačního systému SUSE BYOS). V procesu nasazení nebudete definovat název DNS a nepoužívejte statické IP adresy.

![Snímek obrazovky nasazení virtuálního počítače pro SMT server](./media/hana-installation/image3_vm_deployment.png)

Nasazeného virtuálního počítače je menší a máte ve virtuální síti Azure 10.34.1.4 interní IP adresa. Název virtuálního počítače je *smtserver*. Po instalaci se kontroluje připojení k velká Instance HANA jednotku nebo jednotky. V závislosti na uspořádání překlad potřebujete nakonfigurovat překlad velká Instance HANA jednotek v atd nebo hostitele virtuálních počítačů Azure. 

Přidání disku do virtuálního počítače. Použít tento disk pro uchování aktualizace a spouštěcí disk může být příliš malá. Tady je teď disk připojený k /srv/www/htdocs, jak je znázorněno na následujícím snímku obrazovky. 100 GB disk by měl stačit.

![Snímek obrazovky nasazení virtuálního počítače pro SMT server](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Přihlaste se k velká Instance HANA jednotku nebo jednotky, Udržovat/etc/hosts a zkontrolujte, zda virtuální počítač Azure, který se má spustit SMT server přes síť mohou mít přístup.

Po této kontrole Přihlaste se na virtuálním počítači Azure, který by měl spustit na serveru SMT. Pokud používáte klienta putty k přihlášení k virtuálnímu počítači, spusťte tuto sekvenci příkazů v okně bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Restartujte vaše prostředí bash a aktivovat nastavení. Potom spusťte YAST.

Připojte svůj virtuální počítač (smtserver) k webu SUSE.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Jakmile je virtuální počítač je připojen k serveru SUSE, nainstalujte balíčky smt. Použijte následující příkaz putty smt balíčky nainstalovat.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


Také vám pomůže nástroj YAST smt balíčky nainstalovat. V YAST, přejděte na **údržba softwaru**a vyhledejte smt. Vyberte **smt**, které se automaticky přepne do yast2 smt.

![Snímek obrazovky SMT v YAST](./media/hana-installation/image5_smt_in_yast.PNG)


Přijměte výběr pro instalaci smtserver. Po dokončení instalace, přejděte na konfigurace SMT serveru. Zadejte organizační údaje v Centru pro SUSE zákazníka, který jste získali dříve. Jako adresu URL serveru SMT také zadejte název hostitele vašeho virtuálního počítače Azure. V této ukázce má https://smtserver.

![Snímek obrazovky SMT konfigurace serveru](./media/hana-installation/image6_configuration_of_smtserver1.png)

Teď otestujte, jestli funguje připojení k centru pro zákazníka SUSE. Jak vidíte na následujícím snímku obrazovky v tomto případě ukázka fungovalo.

![Snímek obrazovky testování připojení k Centrum zákazníků SUSE](./media/hana-installation/image7_test_connect.png)

Po spuštění instalace SMT, zadejte heslo databáze. Protože se jedná o novou instalaci, byste měli definovat toto heslo, jak je znázorněno na následujícím snímku obrazovky.

![Snímek obrazovky definování heslo pro databázi](./media/hana-installation/image8_define_db_passwd.PNG)

Dalším krokem je vytvoření certifikátu.

![Snímek obrazovky vytvoření certifikátu pro SMT server](./media/hana-installation/image9_certificate_creation.PNG)

Na konci konfigurace může trvat několik minut, než spustíte kontrolu synchronizace. Po instalaci a konfiguraci serveru SMT, měli byste najít adresář úložiště v rámci přípojný bod /srv/www/htdocs /. Existují také některé podadresářích v úložišti. 

Restartujte SMT server a souvisejících služeb pomocí následujících příkazů.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Stáhnout balíčky do serveru SMT

Po restartují se všechny služby, vyberte odpovídající balíčky ve správě SMT pomocí YAST. Výběr balíčku závisí na bitovou kopii operačního systému serveru velká Instance HANA. Výběr balíčku nezávisí na SLES verzi nebo verzi virtuálního počítače spuštěného SMT serveru. Následující snímek obrazovky ukazuje příklad na obrazovku pro výběr.

![Snímek obrazovky s výběrem balíčky](./media/hana-installation/image10_select_packages.PNG)

Pak můžete začněte počáteční kopii výběr balíčků SMT serveru, který nastavíte. Tato kopie se aktivuje v prostředí s použitím příkazu smt – zrcadlení.

![Snímek obrazovky stahování balíčků SMT server](./media/hana-installation/image11_download_packages.PNG)

Balíčky by se zkopíruje do adresáře vytvořené v rámci přípojný bod /srv/www/htdocs. Tento proces může trvat hodinu i déle v závislosti na tom, kolik balíčky, které jste vybrali. Když tento proces dokončí, přesune SMT instalace klienta. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Nastavení klienta SMT u jednotek pro velké Instance HANA

Klient nebo klienty v tomto případě jsou jednotky velká Instance HANA. Nastavení serveru SMT kopírovat skript clientSetup4SMT.sh do virtuálních počítačů Azure. Kopii, která skript, který přes jednotkou velká Instance HANA jste se chcete připojit k serveru SMT. Spuštění skriptu s parametrem -h a zadejte název serveru SMT jako parametr. V tomto příkladu je název *smtserver*.

![Snímek obrazovky konfigurace SMT klienta](./media/hana-installation/image12_configure_client.PNG)

Je možné, že načíst certifikát ze serveru, klient bude úspěšné, ale registrace nezdaří, jak je znázorněno na následujícím snímku obrazovky.

![Snímek obrazovky registrace klienta se nezdařilo](./media/hana-installation/image13_registration_failed.PNG)

Pokud registrace nepovede, přečtěte si téma [SUSE podporují dokumentu](https://www.suse.com/de-de/support/kb/doc/?id=7006024), a spusťte postupu existuje.

> [!IMPORTANT] 
> Název serveru, zadejte název virtuálního počítače (v tomto případě *smtserver*), aniž by plně kvalifikovaný název domény. 

Po dokončení těchto kroků, spusťte následující příkaz na jednotce velká Instance HANA:

```
SUSEConnect –cleanup
```

> [!Note] 
> Počkejte pár minut po provedení tohoto kroku. Pokud spustíte clientSetup4SMT.sh okamžitě, může se objevit chyba.

Pokud narazíte na problém, který je potřeba opravit, podle kroků v článku SUSE, restartujte clientSetup4SMT.sh na jednotce velká Instance HANA. Teď by měl proběhnout úspěšně.

![Snímek obrazovky úspěšné registrace klienta](./media/hana-installation/image14_finish_client_config.PNG)

Můžete nakonfigurovat klienta SMT velká Instance HANA jednotky pro připojení k serveru SMT, který jste nainstalovali ve virtuálním počítači Azure. Nyní můžete chtít nainstalovat aktualizace operačního systému na velkých instancích HANA "zypperu nahoru" nebo "zypperu v", nebo nainstalovat další balíčky. Můžete získat jenom aktualizace, které jste stáhli dříve na SMT serveru.

## <a name="next-steps"></a>Další postup
- [Instalace HANA na HLI](hana-example-installation.md).











