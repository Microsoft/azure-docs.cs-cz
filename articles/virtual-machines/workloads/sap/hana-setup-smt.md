---
title: Jak nastavit server SMT pro SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
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
ms.openlocfilehash: 699a8a69621642d07d3547c07bb20c0d32ca7686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616993"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Nastavení Serveru SMT pro SUSE Linux
Velké instance SAP HANA nemají přímé připojení k internetu. Není to jednoduchý proces zaregistrovat takovou jednotku u poskytovatele operačního systému a stáhnout a použít aktualizace. Řešením pro SUSE Linux je nastavení serveru SMT ve virtuálním počítači Azure. Hostuj virtuální počítač ve virtuální síti Azure, která je připojená k velké instanci HANA. S takovým serverem SMT může jednotka velké instance HANA zaregistrovat a stáhnout aktualizace. 

Další dokumentaci k SUSE najdete v jejich [nástroji pro správu předplatného pro sles 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Požadavky pro instalaci serveru SMT, který splňuje úlohu pro velké instance HANA, jsou:

- Virtuální síť Azure, která je připojena k okruhu HANA Large Instance ExpressRoute.
- Účet SUSE, který je přidružen k organizaci. Organizace by měla mít platné předplatné SUSE.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Instalace serveru SMT do virtuálního počítače Azure

Nejprve se přihlaste do [zákaznického centra SUSE](https://scc.suse.com/).

Přejděte **Organization** > na**pověření organizace .** V této části byste měli najít pověření, které jsou nezbytné pro nastavení serveru SMT.

Potom nainstalujte virtuální počítač SUSE Linux ve virtuální síti Azure. Chcete-li nasadit virtuální počítač, vezměte sles 12 SP2 galerie image Azure (vyberte BYOS SUSE image). V procesu nasazení nedefinujte název DNS a nepoužívejte statické IP adresy.

![Snímek obrazovky s nasazením virtuálního počítače pro server SMT](./media/hana-installation/image3_vm_deployment.png)

Nasazený virtuální počítač je menší a získal interní IP adresu ve virtuální síti Azure 10.34.1.4. Název virtuálního počítače je *smtserver*. Po instalaci je zkontrolováno připojení k jednotce nebo jednotkám velké instance HANA. V závislosti na tom, jak jste uspořádali překlad názvů, možná budete muset nakonfigurovat rozlišení jednotek velké instance HANA v p.rv.o. virtuálního počítače Azure. 

Přidejte disk do virtuálního počítače. Tento disk slouží k uložení aktualizací a spouštěcí disk sám může být příliš malý. Zde se disk namontoval na /srv/www/htdocs, jak je znázorněno na následujícím snímku obrazovky. Disk o velikosti 100 GB by měl stačit.

![Snímek obrazovky s nasazením virtuálního počítače pro server SMT](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Přihlaste se k jednotce nebo jednotkám velké instance HANA, udržujte /etc/hosts a zkontrolujte, jestli se můžete dostat k virtuálnímu počítači Azure, který má spouštět server SMT v síti.

Po této kontrole se přihlaste k virtuálnímu počítači Azure, který by měl spustit server SMT. Pokud používáte tmel pro přihlášení k virtuálnímu počítači, spusťte tuto sekvenci příkazů v okně bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Restartujte bash pro aktivaci nastavení. Pak spusťte YAST.

Připojte virtuální počítač (smtserver) k webu SUSE.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Po připojení virtuálního počítače k webu SUSE nainstalujte balíčky SMT. K instalaci balíčků smt použijte následující příkaz tmelu.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


K instalaci balíčků SMT můžete také použít nástroj YAST. V YAST přejděte na **Údržbu softwaru**a vyhledejte smt. Vyberte **smt**, který se automaticky přepne na yast2-smt.

![Snímek obrazovky s SMT v YAST](./media/hana-installation/image5_smt_in_yast.PNG)


Přijměte výběr pro instalaci na smtserver. Po dokončení instalace přejděte na konfiguraci serveru SMT. Zadejte pověření organizace ze zákaznického centra SUSE, které jste načetli dříve. Také zadejte název hostitele virtuálního počítače Azure jako adresu URL serveru SMT. V této demonstraci je\/https: /smtserver.

![Snímek obrazovky s konfigurací serveru SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Nyní otestujte, zda připojení k zákaznickému centru SUSE funguje. Jak vidíte na následujícím snímku obrazovky, v tomto demonstračním případě to fungovalo.

![Snímek obrazovky s testovacím připojením k zákaznickému centru SUSE](./media/hana-installation/image7_test_connect.png)

Po spuštění instalace SMT zadejte heslo databáze. Vzhledem k tomu, že se jedná o novou instalaci, měli byste definovat toto heslo, jak je znázorněno na následujícím snímku obrazovky.

![Snímek obrazovky s definováním hesla pro databázi](./media/hana-installation/image8_define_db_passwd.PNG)

Dalším krokem je vytvoření certifikátu.

![Snímek obrazovky s vytvořením certifikátu pro server SMT](./media/hana-installation/image9_certificate_creation.PNG)

Na konci konfigurace může trvat několik minut ke spuštění kontroly synchronizace. Po instalaci a konfiguraci serveru SMT byste měli najít úložiště adresáře pod přípojnou položkou /srv/www/htdocs/. Tam jsou také některé podadresáře v rámci repo. 

Pomocí těchto příkazů restartujte server SMT a související služby.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Stažení balíčků na SMT server

Po restartování všech služeb vyberte příslušné balíčky ve správě SMT pomocí YAST. Výběr balíčku závisí na bitové kopii operačního systému serveru velké instance HANA. Výběr balíčku nezávisí na vydání SLES nebo verzi virtuálního počítače se serverem SMT. Následující snímek obrazovky ukazuje příklad obrazovky výběru.

![Snímek obrazovky s výběrem balíčků](./media/hana-installation/image10_select_packages.PNG)

Dále spusťte počáteční kopii vybraných balíčků na server SMT, který jste nastavili. Tato kopie se aktivuje v prostředí pomocí příkazu smt-mirror.

![Snímek obrazovky se stahováním balíčků na server SMT](./media/hana-installation/image11_download_packages.PNG)

Balíčky by měly být zkopírovány do adresářů vytvořených pod přípojný bod /srv/www/htdocs. Tento proces může trvat hodinu nebo více, v závislosti na tom, kolik balíčků vyberete. Po dokončení tohoto procesu přejděte na nastavení klienta SMT. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Nastavení klienta SMT na jednotkách velké instance HANA

Klient nebo klienti v tomto případě jsou jednotky velké instance HANA. Nastavení serveru SMT zkopírovalo skript clientSetup4SMT.sh do virtuálního počítače Azure. Zkopírujte tento skript do jednotky velké instance HANA, kterou chcete připojit k serveru SMT. Spusťte skript s volbou -h a jako parametr uveďte název serveru SMT. V tomto příkladu je název *smtserver*.

![Snímek obrazovky s konfigurací klienta SMT](./media/hana-installation/image12_configure_client.PNG)

Je možné, že zatížení certifikátu ze serveru klientem proběhne úspěšně, ale registrace se nezdaří, jak je znázorněno na následujícím snímku obrazovky.

![Snímek obrazovky s chybou registrace klienta](./media/hana-installation/image13_registration_failed.PNG)

Pokud se registrace nezdaří, přečtěte si [informace o podpoře SUSE](https://www.suse.com/de-de/support/kb/doc/?id=7006024)a spusťte zde popsané kroky.

> [!IMPORTANT] 
> Pro název serveru zadejte název virtuálního počítače (v tomto případě *smtserver*), bez plně kvalifikovaný název domény. 

Po spuštění těchto kroků spusťte následující příkaz v jednotce velké instance HANA:

```
SUSEConnect –cleanup
```

> [!Note] 
> Počkejte několik minut po tomto kroku. Pokud spustíte clientSetup4SMT.sh okamžitě, může se stát, že dojde k chybě.

Pokud narazíte na problém, který je třeba opravit na základě kroků článku SUSE, restartujte clientSetup4SMT.sh v jednotce velké instance HANA. Nyní by měl úspěšně skončit.

![Snímek obrazovky s úspěšností registrace klienta](./media/hana-installation/image14_finish_client_config.PNG)

Nakonfigurovali jste klienta SMT jednotky velké instance HANA pro připojení k serveru SMT, který jste nainstalovali ve virtuálním počítači Azure. Nyní můžete vzít 'zypper up' nebo 'zypper in' k instalaci aktualizací operačního systému hana velké instance, nebo nainstalovat další balíčky. Aktualizace, které jste stáhli dříve na serveru SMT, můžete získat pouze aktualizace, které jste stáhli dříve.

## <a name="next-steps"></a>Další kroky
- [HANA Instalace na HLI](hana-example-installation.md).











