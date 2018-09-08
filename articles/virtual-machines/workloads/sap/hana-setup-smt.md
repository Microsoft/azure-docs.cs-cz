---
title: Postup instalace serveru SMT pro SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Jak nastavit SMT server pro SAP HANA v Azure (velké Instance).
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
ms.openlocfilehash: 5e729c2e3a802df15973fc6a43ee42265d1de654
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164710"
---
# <a name="setting-up-smt-server-for-suse-linux"></a>Nastavení serveru SMT operačním systémem SUSE Linux
Velké instance SAP HANA nemají přímé připojení k Internetu. Proto není jednoduchý proces registrace jako jednotek s operačním systémem zprostředkovatele a ke stažení a použití opravy. Pokud operačním systémem SUSE Linux jedním řešením může být nastavení serveru SMT ve Virtuálním počítači Azure. Vzhledem k tomu virtuálního počítače Azure musí být hostované ve virtuální síti Azure, která je připojena k velká Instance HANA. Pomocí těchto SMT serveru může velká Instance HANA jednotku zaregistrujte a stažení oprav. 

SUSE poskytuje větší návod na jejich [nástroj pro správu předplatného pro SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Jako předpoklad instalace SMT serveru, který splňuje požadavky úlohy pro velké Instance HANA potřebujete:

- Virtuální síť Azure, který je připojený k okruhu ER velké Instance HANA.
- SUSE účet, který je spojen s organizací. Vzhledem k tomu třeba, aby měl některé platné předplatné SUSE organizace.

## <a name="installation-of-smt-server-on-azure-vm"></a>Instalace SMT serveru na virtuálním počítači Azure

V tomto kroku nainstalujete server SMT ve Virtuálním počítači Azure. První z měr se pro přihlášení k [centrum zákazníků SUSE](https://scc.suse.com/)

Protože jste přihlášeni, přejděte na organizaci--> přihlašovacími údaji organizace. V této části byste měli najít přihlašovací údaje, které jsou potřebné k nastavení SMT serveru.

Třetím krokem je instalace virtuálního počítače s operačním systémem SUSE Linux ve virtuální síti Azure. Pokud chcete nasadit virtuální počítač, proveďte SLES 12 SP2 image z Galerie Azure. V procesu nasazení nebudete definovat název DNS a nepoužívejte statické IP adresy, jak je vidět na tomto snímku obrazovky

![nasazení virtuálního počítače pro SMT server](./media/hana-installation/image3_vm_deployment.png)

Nasazený virtuální počítač byl menších virtuálních počítačů a máte ve virtuální síti Azure z 10.34.1.4 interní IP adresa. Název virtuálního počítače byla smtserver. Po dokončení instalace došlo k zaškrtnutí připojení k HANA velké instance jednotek. Závislé na uspořádání překlad můžete potřebovat konfigurace řešení velká Instance HANA jednotek v etc/hostitele virtuálního počítače Azure. Přidejte další disk k virtuálnímu počítači, který se použije k uložení opravy. Spouštěcí disk může být příliš malá. V tomto případě jsme vám ukázali máte disk připojený k /srv/www/htdocs, jak je znázorněno na následujícím snímku obrazovky. 100 GB disk by měl stačit.

![nasazení virtuálního počítače pro SMT server](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Přihlaste se k jednotek velká Instance HANA, Udržovat/etc/hosts a zkontrolujte, zda virtuální počítač Azure, který se má spustit SMT server přes síť mohou mít přístup.

Poté, co tato kontrola se úspěšně dokončí, budete muset přihlásit k virtuálnímu počítači Azure, který by měl spustit SMT serveru. Pokud používáte klienta putty k přihlášení k virtuálnímu počítači, je nutné provést toto pořadí příkazů v okně bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Po spuštění těchto příkazů, restartujte vaše prostředí bash a aktivovat nastavení. Potom spusťte YAST.

V YAST přejděte na údržbu softwaru a vyhledejte smt. Vyberte smt, automaticky přepnout do yast2 smt, jak je znázorněno níže

![SMT v yast](./media/hana-installation/image5_smt_in_yast.PNG)


Přijměte výběr pro instalaci smtserver. Po instalaci, přejděte na konfigurace serveru SMT a zadejte firemní přihlašovací údaje v Centru pro SUSE zákazníka, který jste získali dříve. Jako adresu URL serveru SMT také zadejte název hostitele vašeho virtuálního počítače Azure. V této ukázce se https://smtserver se zobrazuje další grafiky.

![Konfigurace serveru SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Jako další krok je potřeba otestovat, jestli funguje připojení k centru SUSE zákazníka. Jak vidíte na následujících obrázcích v tomto případě ukázka, fungovalo.

![Test připojení ke službě Center zákazníků operačním systémem SUSE](./media/hana-installation/image7_test_connect.png)

Jednou SMT instalační program spustí, musíte zadat heslo databáze. Protože se jedná o novou instalaci, budete muset definovat toto heslo, jak je znázorněno v další grafiky.

![Definujte heslo pro databázi](./media/hana-installation/image8_define_db_passwd.PNG)

Další interakce, které máte je, když se vytvoří certifikát. Projděte si dialogového okna, jak je ukázáno dále a pokračovat v kroku.

![Vytvoření certifikátu pro SMT server](./media/hana-installation/image9_certificate_creation.PNG)

Může existovat několik minut stráví v kroku "Spustit kontrola synchronizace" na konci konfigurace. Po instalaci a konfiguraci serveru SMT, měli byste najít adresář úložiště v rámci přípojný bod /srv/www/htdocs/plus některé podadresářích v úložišti. 

Restartujte SMT server a souvisejících služeb pomocí následujících příkazů.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-of-packages-onto-smt-server"></a>Stažení balíčků na serveru SMT

Po restartují se všechny služby, vyberte ve správě SMT pomocí Yast odpovídající balíčky. Výběr balíčku závisí na image operačního systému serveru velká Instance HANA a ne na SLES verze nebo verze serverem SMT virtuálního počítače. Níže je uveden příklad výběr obrazovky.

![Vybrat balíčky](./media/hana-installation/image10_select_packages.PNG)

Jakmile budete hotovi s výběrem balíčku, musíte spustit počáteční kopii výběr balíčků SMT serveru, který nastavíte. Tato kopie se aktivuje v prostředí shell pomocí příkazu smt – zrcadlení jak je znázorněno níže


![Stáhnout balíčky SMT server](./media/hana-installation/image11_download_packages.PNG)

Jak vidíte výše, by se do adresáře vytvořené v rámci přípojný bod /srv/www/htdocs zkopíruje balíčky. Tento proces může trvat nějakou dobu. Závisí na počet balíčků, které vyberete, může trvat až jedné hodiny nebo i déle.
Když tento proces dokončí, budete potřebovat přesunout SMT instalace klienta. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Nastavení klienta SMT u jednotek pro velké Instance HANA

Klientů v tomto případě jsou jednotky velká Instance HANA. Nastavení serveru SMT kopírovat skript clientSetup4SMT.sh připojení k virtuálnímu počítači Azure. Kopii, která skript, který přes jednotkou velká Instance HANA jste se chcete připojit k serveru SMT. Spuštění skriptu s parametrem -h a pojmenujte ji jako parametr SMT serveru. V tomto příkladu smtserver.

![Konfigurace klienta SMT](./media/hana-installation/image12_configure_client.PNG)

Mohou existovat scénáře, kde načíst certifikát ze serveru klientovi bylo úspěšné, ale registrace nebyla úspěšná, protože je uvedeno níže.

![Neúspěšné registrace klienta](./media/hana-installation/image13_registration_failed.PNG)

Pokud registrace se nezdařila, přečtěte si tento [SUSE podporují dokumentu](https://www.suse.com/de-de/support/kb/doc/?id=7006024) a proveďte kroky popsané existuje.

> [!IMPORTANT] 
> Jako název serveru budete muset zadat název virtuálního počítače v tomto případu smtserver bez plně kvalifikovaný název domény. Pouze název funguje virtuálního počítače. 

Po provedení těchto kroků je potřeba spuštěním následujícího příkazu na jednotce velká Instance HANA

```
SUSEConnect –cleanup
```

> [!Note] 
> V našich testech jsme museli vždy počkejte pár minut po provedení tohoto kroku. ClientSetup4SMT.sh hned po nápravných opatření popsané v článku SUSE skončilo s zprávy, že certifikát nebude platný ještě. Čeká se obvykle 5 až 10 minut a provádění clientSetup4SMT.sh skončila v konfiguraci úspěšné klienta.

Pokud jste narazili na problém, který je potřebný k opravě podle kroků v článku SUSE, budete muset restartovat clientSetup4SMT.sh na jednotce velká Instance HANA znovu. Nyní ji by měl úspěšně dokončit jak je znázorněno níže.

![Úspěšná registrace klienta](./media/hana-installation/image14_finish_client_config.PNG)

Pro tento krok nakonfigurovat klienta SMT jednotky velká Instance HANA a připojit se SMT serverem, který jste nainstalovali ve virtuálním počítači Azure. Pro instalaci oprav operačního systému na velkých instancích HANA nebo nainstalovat další balíčky můžete využít nyní "zypperu nahoru" nebo "zypperu v". Předpokládá se, že pouze můžete získat opravy, které jste stáhli dříve na serveru SMT.

**Další kroky**
- Přečtěte si [instalace HANA na HLI](hana-example-installation.md).











