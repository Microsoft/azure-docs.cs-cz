---
title: Instalace Defenderu pro IoT
description: Přečtěte si, jak nainstalovat senzor a místní konzolu pro správu pro Azure Defender pro IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/2/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 175f006adbc18d21e0f0c5d859737aedb00bc457
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574658"
---
# <a name="defender-for-iot-installation"></a>Instalace Defenderu pro IoT

Tento článek popisuje, jak nainstalovat následující prvky Azure Defenderu pro IoT:

- **Senzor**: Defender pro senzory IoT shromažďuje síťový provoz ICS pomocí pasivního monitorování (bez agentů). Pasivní a nerušivé, senzory mají žádný vliv na sítě a a zařízení IoT. Senzor se připojí k portu SPAN nebo k síti KLEPNE a hned začne monitorovat vaši síť. V konzole senzoru se objevují detekce. Zde je můžete zobrazit, prozkoumat a analyzovat v mapě sítě, v inventáři zařízení a v rozsáhlých různých sestavách. Mezi příklady patří sestavy posouzení rizik, dotazy dolování dat a vektory útoku. Přečtěte si další informace o schopnostech senzorů v tématu [uživatelská příručka ke snímači IoT pro uživatele (přímé stažení)](./getting-started.md).

- Místní **Konzola pro správu**: místní Konzola pro správu umožňuje provádět správu zařízení, řízení rizik a správu ohrožení zabezpečení. Můžete ji také použít k provádění monitorování hrozeb a reakci na incidenty v celém podniku. Nabízí jednotný pohled na všechna síťová zařízení, klíč IoT a rizika a výstrahy zjištěné v zařízeních, kde jsou senzory nasazeny. Pomocí místní konzoly pro správu můžete zobrazit a spravovat senzory v gapped sítích.

Tento článek obsahuje následující informace o instalaci:

  - **Hardware:** Podrobnosti o fyzickém zařízení Dell a HPE.

  - **Software:** Instalace softwaru senzoru a místní konzoly pro správu.

  - **Virtuální zařízení:** Podrobnosti o virtuálním počítači a instalaci softwaru.

Po instalaci připojte snímač k síti.

## <a name="about-defender-for-iot-appliances"></a>O programu Defender pro zařízení IoT 

V následujících částech najdete informace o programu Defender pro zařízení se snímači IoT a o zařízení pro místní konzolu pro správu služby Defender for IoT.

### <a name="physical-appliances"></a>Fyzická zařízení

Snímač programu Defender pro zařízení IoT se připojí k portu SPAN nebo k síti KLEPNE a hned začne shromažďovat síťový provoz ICS pomocí pasivního monitorování (bez agentů). Tento proces má žádný vliv na sítě a zařízení, protože není umístěný v cestě k datům a aktivně nekontroluje zařízení.

K dispozici jsou následující zařízení montovatelné do racku:

| **Typ nasazení** | **Firemní** | **Enterprise** | **SMB** | **Čára** |
|--|--|--|--|--|
| **Modelování** | HPE DL360 | Dell PowerEdge R340 XL | HPE DL20 | HPE DL20 |
| **Porty monitorování** | až 15 RJ45 nebo 8 OPT | až 9 RJ45 nebo 6 OPT | až 8 RJ45 nebo 6 OPT | 4 RJ45 |
| **Maximální šířka pásma\*** | 3 GB/s | 1 GB/s | 1 GB/s | 100 MB/s |
| **Maximální počet chráněných zařízení** | 30 000 | 10 000 | 15 000 | 1 000 |

* Maximální kapacita šířky pásma se může lišit v závislosti na distribuci protokolu.

### <a name="virtual-appliances"></a>Virtuální zařízení

K dispozici jsou následující virtuální spotřebiče:

| **Typ nasazení** | **Enterprise** | **SMB** | **Čára** |
|--|--|--|--|
| **Popis** | Virtuální zařízení pro podniková nasazení | Virtuální zařízení pro nasazení SMB | Virtuální zařízení pro nasazení na lince |
| **Maximální šířka pásma\*** | 150 MB/s | 15 MB/s | 3 MB/s |
| **Maximální počet chráněných zařízení** | 3 000 | 300 | 100 |
| **Typ nasazení** | Enterprise | SMB | Čára |
| **Popis** | Virtuální zařízení pro podniková nasazení | Virtuální zařízení pro nasazení SMB | Virtuální zařízení pro nasazení na lince |

* Maximální kapacita šířky pásma se může lišit v závislosti na distribuci protokolu.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>Specifikace hardwaru pro místní konzolu pro správu

 | Položka | Popis |
 |----|--|
 **Popis** | V architektuře s více vrstvami nabízí místní Konzola pro správu viditelnost a kontrolu nad geograficky rozloženými lokalitami. Integruje se se zásobníky zabezpečení SOC, včetně systémů Siem, systémů vytváření lístků, bran firewall nové generace, zabezpečených platforem vzdáleného přístupu a programu Defender pro sandboxový izolovaný prostor služby ICS pro IoT. |
 **Typ nasazení** | Enterprise |
 **Typ zařízení**  | Dell R340, VM |
 **Počet spravovaných senzorů** | Unlimited |

## <a name="prepare-for-the-installation"></a>Příprava instalace

### <a name="access-the-iso-installation-image"></a>Přístup k imagi instalace ISO

Bitová kopie instalace je přístupná z programu Defender pro IoT Portal.

Přístup k souboru:

1. Přihlaste se ke svému účtu Defender pro IoT.

1. Otevřete stránku **snímač sítě** nebo místní **Konzola pro správu** a vyberte verzi, kterou chcete stáhnout.

### <a name="install-from-dvd"></a>Instalace z disku DVD

Před instalací se ujistěte, že máte:

- Přenosná jednotka DVD s konektorem USB.

- Bitová kopie instalačního programu ISO

Instalace:

1. Vypálení obrázku na disk DVD nebo Příprava disku na klíč. Připojte k počítači přenosnou jednotku DVD, klikněte pravým tlačítkem na bitovou kopii ISO a vyberte **vypálit na disk**.

1. Připojte disk DVD nebo disk k klíči a nakonfigurujte zařízení tak, aby se spouštělo z disku DVD nebo disku na klíč.

### <a name="install-from-disk-on-a-key"></a>Instalace z disku na klíč

Před instalací se ujistěte, že máte:

  - Rufus je nainstalovaný.
  
  - Disk na klíč s rozhraním USB verze 3,0 a novějším. Minimální velikost je 4 GB.

  - Soubor bitové kopie instalačního programu ISO.

Disk na klíči bude v tomto procesu smazán.

Příprava disku na klíč:

1. Spusťte Rufus a vyberte **senzor ISO**.

1. Připojte disk k tomuto přednímu panelu na klíč.

1. Nastavte systém BIOS serveru tak, aby se spouštěl z USB.

## <a name="dell-poweredger340xl-installation"></a>Instalace Dell PowerEdgeR340XL

Před instalací softwaru na zařízení Dell je potřeba upravit konfiguraci systému BIOS zařízení:

  - [Přední panel Dell PowerEdge R340](#dell-poweredge-r340-front-panel) a [zadní panel Dell PowerEdge R340](#dell-poweredge-r340-back-panel) obsahuje popis front-end panelů a informace vyžadované pro instalaci, jako jsou ovladače a porty.

  - [Konfigurace Dell BIOS](#dell-bios-configuration) poskytuje informace o tom, jak se připojit k rozhraní pro správu zařízení Dell a nakonfigurovat systém BIOS.

  - [Instalace softwaru (Dell R340)](#software-installation-dell-r340) popisuje postup potřebný k instalaci programu Defender pro software IoT snímače.

### <a name="dell-poweredge-r340xl-requirements"></a>Požadavky na Dell PowerEdge R340XL 

K instalaci zařízení Dell PowerEdge R340XL potřebujete:

- Podniková licence pro kontroler vzdáleného přístupu Dell (iDrac)

- XML konfigurace systému BIOS

- Verze firmwaru serveru:

  - 2.1.6 verze systému BIOS

  - iDrac verze 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Přední panel Dell PowerEdge R340

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Přední panel Dell PowerEdge R340":::

 1. Levý ovládací panel 
 1. Optická jednotka (volitelné) 
 1. Ovládací panel vpravo 
 1. Informační značka 
 1. Drives  

### <a name="dell-poweredge-r340-back-panel"></a>Panel back-R340 Dell PowerEdge

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Panel back-R340 Dell PowerEdge.":::

1. Sériový port 
1. Port síťové karty (GB 1) 
1. Port síťové karty (GB 1) 
1. Poloviční výška – PCIe 
1. Slot pro rozšiřující karty PCIe na celou výšku 
1. Zdroj napájení – jednotka 1 
1. Zdroj napájení 2. jednotka 
1. Identifikace systému 
1. Tlačítko portu kabelu indikátoru stavu systému (CMA) 
1. Port USB 3,0 (2) 
1. iDRAC9 vyhrazený síťový port 
1. Port VGA 

### <a name="dell-bios-configuration"></a>Konfigurace Dell BIOS

Pro úpravu zařízení Dell pro práci se softwarem se vyžaduje konfigurace Dell BIOS.

Konfigurace systému BIOS se provádí pomocí předdefinované konfigurace. Soubor je přístupný z centra pro [technickou podporu](https://help.cyberx-labs.com/).

Importujte konfigurační soubor do zařízení Dell. Před použitím konfiguračního souboru je potřeba navázat komunikaci mezi zařízením Dell a počítačem pro správu.

Zařízení Dell se spravuje pomocí integrovaného iDRACu s řadičem pro životní cyklus (LC). LC je vložená do každého serveru Dell PowerEdge a poskytuje funkce, které vám pomůžou nasadit, aktualizovat, monitorovat a udržovat zařízení Dell PowerEdge.

Aby bylo možné navázat komunikaci mezi zařízením Dell a počítačem pro správu, je třeba zadat IP adresu iDRAC a IP adresu počítače pro správu ve stejné podsíti.

Po navázání spojení se systém BIOS dá konfigurovat.

Konfigurace systému Dell BIOS:

1. [Konfigurace IP adresy iDRAC](#configure-idrac-ip-address)

1. [Import konfiguračního souboru systému BIOS](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>Konfigurace IP adresy iDRAC

1. Senzor zapněte.

1. Pokud je operační systém již nainstalován, vyberte klávesu F2 a zadejte konfiguraci systému BIOS.

1. Vyberte **Nastavení iDRAC**.

1. Vyberte **síť**.

   > [!NOTE]
   > Během instalace musíte nakonfigurovat výchozí IP adresu a heslo iDRAC uvedené v následujících krocích. Po instalaci tyto definice změníte.

1. Změňte statickou IPv4 adresu na **10.100.100.250**.

1. Změňte masku statické podsítě na **255.255.255.0**.

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="Snímek obrazovky zobrazující masku statické podsítě":::

1. Vyberte **back**-  >  **Finish (Dokončit**).

#### <a name="import-the-bios-configuration-file"></a>Import konfiguračního souboru systému BIOS

Tento článek popisuje, jak nakonfigurovat systém BIOS pomocí konfiguračního souboru.

1. Připojte počítač se statickou předem nakonfigurovanou IP adresou **10.100.100.200** na port **iDRAC** .

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="Snímek obrazovky předkonfigurovaného portu IP adres.":::

1. Otevřete prohlížeč a zadejte **10.100.100.250** pro připojení k webovému rozhraní iDRAC.

1. Přihlaste se pomocí výchozích oprávnění správce Dell:

   - Uživatelské jméno: **root**

   - Heslo: **Calvin**

1. Přihlašovací údaje zařízení jsou:

   - Uživatelské jméno: **xxx**

   - Heslo: **xxx**

     Je zahájena operace importovat profil serveru.

     > [!NOTE]
     > Před importem souboru se ujistěte, že:
     > - Jste jediným uživatelem, který je aktuálně připojený k iDRAC.
     > - Systém není v nabídce systému BIOS.

1. Přejít na konfigurační profil **konfiguračního**  >  **serveru**. Nastavte následující parametry:

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="Snímek obrazovky zobrazující konfiguraci vašeho profilu serveru.":::

   | Parametr | Konfigurace |
   |--|--|
   | Typ umístění | Vyberte **místní**. |
   | Cesta k souboru | Vyberte **zvolit soubor** a přidejte konfigurační soubor XML. |
   | Importovat součásti | Vyberte možnost **BIOS, síťová karta, RAID**. |
   | Maximum wait time | Vyberte **20 minut**. |

1. Vyberte **Importovat**.

1. Pokud chcete tento proces monitorovat, klikněte na  >  **fronta úloh** údržby.

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="Snímek obrazovky, který zobrazuje frontu úloh.":::

#### <a name="manually-configuring-bios"></a>Ruční konfigurace systému BIOS 

Musíte ručně nakonfigurovat systém BIOS zařízení, pokud:

- Nekoupili jste zařízení od šipky.

- Máte zařízení, ale nemáte přístup k konfiguračnímu souboru XML.

Po přístupu k systému BIOS přejděte do části **nastavení zařízení**.

Postup ruční konfigurace:

1. Přístup k systému BIOS zařízení můžete získat přímo pomocí klávesnice a obrazovky nebo pomocí iDRAC.

   - Pokud zařízení není Defender pro zařízení IoT, otevřete prohlížeč a pokračujte na IP adresu, která byla nakonfigurovaná dřív. Přihlaste se s oprávněními výchozích správců Dell. Pro uživatelské jméno a **Calvin** pro heslo použijte **kořen** .

   - Pokud je zařízení Defenderem pro zařízení IoT, přihlaste se pomocí **xxx** pro uživatelské jméno a **xxx** pro heslo.

1. Po přístupu k systému BIOS přejděte do části **nastavení zařízení**.

1. Zvolte konfiguraci řízenou pomocí RAID výběrem možnosti **integrovaný řadič RAID 1: Nástroj Dell PERC \<PERC H330 Adapter\> Configuration Utility**.

1. Vyberte možnost **Správa konfigurace**.

1. Vyberte **vytvořit virtuální disk**.

1. V poli **Vybrat úroveň RAID** vyberte možnost **RAID5**. Do pole **název virtuálního disku** zadejte **root** a vyberte **fyzické disky**.

1. Vyberte možnost **zaškrtnout vše** a pak vyberte **použít změny** .

1. Vyberte **OK**.

1. Posuňte se dolů a vyberte **vytvořit virtuální disk**.

1. Zaškrtněte políčko **Potvrdit** a vyberte **Ano**.

1. Vyberte **OK**.

1. Vraťte se na hlavní obrazovku a vyberte **systém BIOS**.

1. Vyberte **Nastavení spouštění**.

1. V možnosti **spouštěcí režim** vyberte **BIOS**.

1. Vyberte **zpět** a pak výběrem možnosti **Dokončit** ukončete nastavení systému BIOS.

### <a name="software-installation-dell-r340"></a>Instalace softwaru (Dell R340)

Proces instalace trvá přibližně 20 minut. Po dokončení instalace se systém několikrát restartuje.

Instalace:

1. Ověřte, zda je médium verze připojeno k zařízení jedním z následujících způsobů:

   - Připojte externí disk CD nebo disk k klíči s verzí.

   - Připojte bitovou kopii ISO pomocí iDRAC. Po přihlášení k iDRAC vyberte virtuální konzolu a pak vyberte **virtuální Médium**.

1. V části **mapový disk CD/DVD** vyberte **zvolit soubor**.

1. V otevřeném dialogovém okně vyberte soubor bitové kopie ISO verze pro tuto verzi.

1. Vyberte tlačítko **připojit zařízení** .

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="Snímek obrazovky zobrazující mapované zařízení":::

1. Médium je připojené. Vyberte **Zavřít**.

1. Spusťte zařízení. Pokud používáte iDRAC, můžete restartovat servery tak, že vyberete **ovládací tlačítko Consul** . Potom v **makrech klávesnice** vyberte tlačítko **použít** , ve kterém se spustí posloupnost kláves CTRL + ALT + DELETE.

1. Vyberte **angličtinu**.

1. Vyberte **senzor – Release- \<version\> Enterprise**.

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Snímek obrazovky zobrazující výběr verze":::   

1. Zadejte profil zařízení a vlastnosti sítě:

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="Snímek obrazovky, který zobrazuje profil zařízení.":::   

   | Parametr | Konfigurace |
   |--|--|
   | **Hardwarový profil** | **společnosti** |
   | **Rozhraní pro správu** | **eno1** |
   | **Síťové parametry (poskytované zákazníkem)** | - |
   |**IP adresa sítě pro správu:** | - |
   | **Maska podsítě:** | - |
   | **název hostitele zařízení:** | - |
   | **NÁZV** | - |
   | **IP adresa výchozí brány:** | - |
   | **Vstupní rozhraní:** |  Systém vygeneruje seznam vstupních rozhraní za vás. Chcete-li zrcadlit vstupní rozhraní, zkopírujte všechny položky, které jsou uvedeny v seznamu, pomocí oddělovače čárky. Nemusíte konfigurovat rozhraní mostu. Tato možnost se používá jenom pro zvláštní případy použití. |

1. Po asi 10 minutách se zobrazí dvě sady přihlašovacích údajů. Jedna je určena pro uživatele **CyberX** a jedna je pro uživatele **podpory** .  

1. Uložte ID zařízení a hesla. Tyto přihlašovací údaje budete potřebovat pro přístup k platformě při jejím prvním použití.

1. Pokračujte výběrem **ENTER** .

## <a name="hpe-proliant-dl20-installation"></a>HPE instalace DL20

Tento článek popisuje HPE proces instalace DL20, který zahrnuje následující kroky:

  - Povolte vzdálený přístup a aktualizujte výchozí heslo správce.
  - Nakonfigurujte nastavení systému BIOS a RAID.
  - Nainstalujte software.

### <a name="about-the-installation"></a>O instalaci

  - Zařízení s podnikem a SMB je možné nainstalovat. Proces instalace je stejný pro oba typy zařízení, s výjimkou konfigurace pole.
  - Je k dispozici výchozí uživatel s právy pro správu. Doporučujeme změnit heslo během procesu konfigurace sítě.
  - Během procesu konfigurace sítě nakonfigurujete port MOP na síťovém portu 1.
  - Proces instalace trvá přibližně 20 minut. Po dokončení instalace se systém několikrát restartuje.

### <a name="hpe-proliant-dl20-front-panel"></a>HPE DL20 přední panel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="HPE (DL20) přední panel.":::

### <a name="hpe-proliant-dl20-back-panel"></a>HPE panel DL20 back

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="Zadní panel HPE DL20.":::

### <a name="enable-remote-access-and-update-the-password"></a>Povolení vzdáleného přístupu a aktualizace hesla

K nastavení možností sítě a aktualizaci výchozího hesla použijte následující postup.

Postup povolení a aktualizace hesla:

1. Připojte obrazovku a klávesnici k zařízení HP, zapněte zařízení a stiskněte **F9**.

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="Snímek obrazovky zobrazující HPE okno":::

1. Přechod do **systémových nástrojů systémové nástroje** konfigurace  >  **systému**  >  Možnosti síť **MOP 5 konfigurační nástroj**  >  **Možnosti sítě**.

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="Snímek obrazovky zobrazující okno Konfigurace systému":::

    1.  Z pole **adaptér síťového rozhraní** vyberte **Shared Network Port-dvěma** .
    
    1.  Zakažte protokol DHCP.
    
    1.  Zadejte IP adresu, masku podsítě a IP adresu brány.

1. Vyberte **F10: Save (Uložit**).

1. Výběrem **klávesy ESC** se vraťte k **konfiguračnímu nástroji MOP 5** a pak vyberte **Správa uživatelů**.

1. Vyberte **Upravit/odebrat uživatele**. Správce je jediným definovaným výchozím uživatelem. 

1. Změňte výchozí heslo a vyberte **F10: Uložit**.

### <a name="configure-the-hpe-bios"></a>Konfigurace systému HPE BIOS

Následující postup popisuje, jak nakonfigurovat HPE BIOS pro zařízení s podnikem a SMB.

Konfigurace systému HPE BIOS:

1. Vyberte **Systémové nástroje** systémové  >  **Konfigurace systém**  >  **BIOS/konfigurace platformy (RBSU)**.

1. Ve formuláři **Konfigurace systému BIOS/platformy (RBSU)** vyberte možnost **Možnosti spuštění**.

1. Změňte **režim spouštění** na **starší verzi režimu systému BIOS** a pak vyberte **F10: Uložit**.

1. Dvojitým kliknutím na tlačítko **ESC** zavřete formulář **Konfigurace systému** .

#### <a name="for-the-enterprise-appliance"></a>Pro podniková zařízení

1. Vyberte **integrovaný RAID 1: HPE Smart Array P408i-konfigurace pole SR gen 10**  >    >  **vytvořit pole**.

1. Ve formuláři **vytvořit pole** vyberte všechny možnosti. K dispozici jsou tři možnosti **podnikového** zařízení.

#### <a name="for-the-smb-appliance"></a>Pro zařízení SMB

1. Vyberte **integrovaný RAID 1: HPE Smart Array P208i-konfigurace pole SR gen 10**  >    >  **vytvořit pole**.

1. Vyberte **pokračovat k dalšímu formuláři**.

1. Ve formuláři **nastavit úroveň RAID** nastavte úroveň na **RAID 5** pro podniková nasazení a **RAID 1** pro nasazení SMB.

1. Vyberte **pokračovat k dalšímu formuláři**.

1. Do formuláře **popisek logické jednotky** zadejte **logickou jednotku 1**.

1. Vyberte **Odeslat změny**.

1. Ve formuláři **Odeslat** vyberte **zpět k hlavní nabídce**.

1. Vyberte **F10: Uložit** a dvakrát stiskněte klávesu **ESC** .

1. V okně **Systémové nástroje** vyberte **jednorázovou spouštěcí nabídku**.

1. V **nabídce jednorázové spuštění** vyberte **starší systém BIOS One-Time spouštěcí nabídka**.

1. Zobrazí se **spouštěcí Windows ve starší verzi** a v oknech pro **přepsání spouštění** . Vyberte možnost přepsání při spuštění; například na prostředí CD-ROM, USB, HDD nebo rozhraní UEFI.

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="Snímek obrazovky, který zobrazuje první okno přepisu spuštění.":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="Snímek obrazovky, který zobrazuje druhé okno přepisu spuštění.":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>Instalace softwaru (zařízení DL20 pro HPE)

Proces instalace trvá přibližně 20 minut. Po dokončení instalace se systém několikrát restartuje.

Postup instalace softwaru:

1. Připojte obrazovku a klávesnici k zařízení a pak se připojte k rozhraní příkazového řádku.

1. Připojte na klíč externí disk CD nebo disk s bitovou kopií ISO, kterou jste stáhli ze stránky **aktualizace** v Defenderu pro IoT Portal.

1. Spusťte zařízení.

1. Vyberte **angličtinu**.

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="Výběr angličtiny v okně rozhraní příkazového řádku":::

1. Vyberte **senzor – Release- <version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Obrazovka obrazovky pro výběr verze":::

1. V Průvodci instalací definujte profil zařízení a vlastnosti sítě:

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Snímek obrazovky, který zobrazuje Průvodce instalací.":::

    | Parametr | Konfigurace |
    | ----------| ------------- |
    | **Hardwarový profil** | Vyberte **Enterprise** nebo **Office** pro nasazení SMB. |
    | **Rozhraní pro správu** | **eno2** |
    | **Výchozí síťové parametry (obvykle jsou parametry poskytovány zákazníkem)** | **IP adresa sítě pro správu:** <br/> <br/>**název hostitele zařízení:** <br/>**NÁZV** <br/>**výchozí IP adresa brány:**|
    | **Vstupní rozhraní:** | Systém vygeneruje seznam vstupních rozhraní za vás.<br/><br/>Chcete-li zrcadlit vstupní rozhraní, zkopírujte všechny položky uvedené v seznamu pomocí oddělovače čárky: **eno5, eno3, eno1, eno6, eno4**<br/><br/>**Pro HPE DL20: do not list eno1, enp1s0f4u4 (rozhraní MOP)**<br/><br/>**Most**: není nutné konfigurovat rozhraní mostu. Tato možnost se používá jenom pro zvláštní případy použití. Pokračujte stisknutím klávesy **Enter**. |

1. Po asi 10 minutách se zobrazí dvě sady přihlašovacích údajů. Jedna je určena pro uživatele **CyberX** a jedna je pro uživatele **podpory** .

1. Uložte si ID a heslo zařízení. K prvnímu přístupu k platformě budete potřebovat přihlašovací údaje.

1. Pokračujte výběrem **ENTER** .

## <a name="hpe-proliant-dl360-installation"></a>HPE instalace DL360

  - Je k dispozici výchozí uživatel s právy pro správu. Doporučujeme změnit heslo během konfigurace sítě.

  - Během konfigurace sítě nakonfigurujete port MOP.

  - Proces instalace trvá přibližně 20 minut. Po dokončení instalace se systém několikrát restartuje.

### <a name="hpe-proliant-dl360-front-panel"></a>HPE DL360 přední panel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="HPE (DL360) přední panel.":::

### <a name="hpe-proliant-dl360-back-panel"></a>HPE panel DL360 back

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="HPE panel DL360 na pozadí.":::

### <a name="enable-remote-access-and-update-the-password"></a>Povolení vzdáleného přístupu a aktualizace hesla

Projděte si předchozí části pro HPE instalaci DL20:

  - "Povolení vzdáleného přístupu a aktualizace hesla"

  - "Konfigurace systému HPE BIOS"

Konfigurace organizace je shodná.

> [!Note]
> Ve formuláři Array (pole) ověřte, že jste vybrali všechny možnosti.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>Vzdálená instalace MOP (z virtuální jednotky)

Tento postup popisuje instalaci nástroje MOP z virtuální jednotky.

Instalace:

1. Přihlaste se ke konzole MOP a pak klikněte pravým tlačítkem na obrazovku servery.

1. Vyberte možnost **Konzola HTML5**.

1. V konzole nástroje vyberte ikonu CD a zvolte možnost CD/DVD.

1. Vyberte **místní soubor ISO**.

1. V dialogovém okně vyberte příslušný soubor ISO.

1. Přejít na ikonu vlevo, vyberte možnost **napájení** a vyberte možnost **resetovat**.

1. Zařízení se restartuje a spustí se proces instalace senzoru.

### <a name="software-installation-hpe-dl360"></a>Instalace softwaru (HPE DL360)

Proces instalace trvá přibližně 20 minut. Po dokončení instalace se systém několikrát restartuje.

Instalace:

1. Připojte obrazovku a klávesnici k zařízení a pak se připojte k rozhraní příkazového řádku.

1. Připojte externí disk CD nebo disk k klíči s bitovou kopií ISO, kterou jste stáhli ze stránky **aktualizace** v Defenderu pro IoT Portal.

1. Spusťte zařízení.

1. Vyberte **angličtinu**.

1. Vyberte **senzor – Release- <version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Snímek obrazovky znázorňující výběr verze":::

1. V Průvodci instalací definujte profil zařízení a vlastnosti sítě.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Snímek obrazovky, který zobrazuje Průvodce instalací.":::

    | Parametr | Konfigurace |
    | ----------| ------------- |
    | **Hardwarový profil** | Vyberte **firemní**. |
    | **Rozhraní pro správu** | **eno2** |
    | **Výchozí síťové parametry (poskytované zákazníkem)** | **IP adresa sítě pro správu:** <br/>**Maska podsítě:** <br/>**název hostitele zařízení:** <br/>**NÁZV** <br/>**výchozí IP adresa brány:**|
    | **Vstupní rozhraní:**  | Systém vygeneruje seznam vstupních rozhraní za vás.<br/><br/>Chcete-li zrcadlit vstupní rozhraní, zkopírujte všechny položky, které jsou uvedeny v seznamu, pomocí oddělovače čárky.<br/><br/> Nemusíte konfigurovat rozhraní mostu. Tato možnost se používá jenom pro zvláštní případy použití. |

1. Po asi 10 minutách se zobrazí dvě sady přihlašovacích údajů. Jedna je určena pro uživatele **CyberX** a jedna je pro uživatele **podpory** .

1. Uložte si ID a heslo zařízení. K prvnímu přístupu k platformě budete potřebovat tato pověření.

1. Pokračujte výběrem **ENTER** .

## <a name="sensor-installation-for-the-virtual-appliance"></a>Instalace senzoru pro virtuální zařízení

Můžete nasadit virtuální počítač pro nástroj Defender pro IoT snímač v následujících architekturách:


| Architektura | Specifikace | Využití | Komentáře |
|---|---|---|---|
| **Enterprise** | PROCESOR: 8<br/>Paměť: 32G RAM<br/>PEVNÝ DISK: 1800 GB | Produkční prostředí | Výchozí a nejběžnější |
| **Malý podnik** | PROCESOR: 4 <br/>Paměť: 8G RAM<br/>PEVNÝ DISK: 500 GB | Testovací nebo malé produkční prostředí | -  |
| **Office** | PROCESOR: 4<br/>Paměť: 8G RAM<br/>PEVNÝ DISK: 100 GB | Malá testovací prostředí | -  |

### <a name="prerequisites"></a>Předpoklady

Místní Konzola pro správu podporuje možnosti nasazení VMware a Hyper-V. Než začnete s instalací, ujistěte se, že máte následující položky:

  - VMware (ESXi 5,5 nebo novější) nebo hypervisor technologie Hyper-V (Windows 10 pro nebo Enterprise) nainstalovaný a funkční

  - Dostupné hardwarové prostředky pro virtuální počítač

  - Instalační soubor ISO pro senzor Azure Defender pro IoT

Ujistěte se, že je hypervisor spuštěný.

### <a name="create-the-virtual-machine-esxi"></a>Vytvořit virtuální počítač (ESXi)

1. Přihlaste se k ESXi, zvolte příslušné **úložiště dat** a pak vyberte **Prohlížeč úložiště dat**.

1. **Nahrajte** obrázek a vyberte **Zavřít**.

1. Přejít na **Virtual Machines** a pak vyberte **vytvořit/zaregistrovat virtuální počítač**.

1. Vyberte **vytvořit nový virtuální počítač** a pak vyberte **Další**.

1. Přidejte název snímače a vyberte:

   - Kompatibilita: **&lt; nejnovější verze &gt; ESXi**

   - Řada hostovaných operačních systémů: **Linux**

   - Verze operačního systému hosta: **Ubuntu Linux (64 bitů)**

1. Vyberte **Další**.

1. Zvolte příslušné úložiště dat a vyberte **Další**.

1. Změňte parametry virtuálního hardwaru podle požadované architektury.

1. V případě **jednotky CD/DVD 1** vyberte **soubor ISO úložiště dat** a vyberte soubor ISO, který jste nahráli dříve.

1. Vyberte **Další** > **Dokončit**.

### <a name="create-the-virtual-machine-hyper-v"></a>Vytvořit virtuální počítač (Hyper-V)

Tento postup popisuje, jak vytvořit virtuální počítač pomocí technologie Hyper-V.

Vytvoření virtuálního počítače:

1. Vytvořte virtuální disk ve Správci technologie Hyper-V.

1. Vyberte **formát = VHDX**.

1. Vyberte **typ = dynamické rozbalování**.

1. Zadejte název a umístění virtuálního pevného disku.

1. Zadejte požadovanou velikost (podle architektury).   

1. Zkontrolujte souhrn a vyberte **Dokončit**.

1. V nabídce **Akce** vytvořte nový virtuální počítač.

1. Zadejte název virtuálního počítače.

1. Vyberte možnost **zadat generaci generace**  >  **1**.

1. Zadejte přidělení paměti (podle architektury) a zaškrtněte políčko pro dynamickou paměť.

1. Nakonfigurujte adaptér sítě podle síťové topologie serveru.

1. Připojte k virtuálnímu počítači soubor VHDX, který jste předtím vytvořili.

1. Zkontrolujte souhrn a vyberte **Dokončit**.

1. Pravým tlačítkem myši klikněte na nový virtuální počítač a vyberte **Nastavení**.

1. Vyberte **Přidat hardware** a přidejte nový síťový adaptér.

1. Vyberte virtuální přepínač, který se připojí k síti pro správu senzorů.

1. Přidělte prostředky procesoru (podle architektury).

1. Připojte bitovou kopii ISO konzoly pro správu k virtuální jednotce DVD.

1. Spusťte virtuální počítač.

2. V nabídce **Akce** vyberte **připojit** a pokračujte v instalaci softwaru.

### <a name="software-installation-esxi-and-hyper-v"></a>Instalace softwaru (ESXi a Hyper-V)

Tato část popisuje instalaci softwaru ESXi a Hyper-V.

Instalace:

1. Otevřete konzolu virtuálního počítače.

1. Virtuální počítač se spustí z image ISO a zobrazí se obrazovka pro výběr jazyka. Vyberte **angličtinu**.

1. Vyberte požadovanou architekturu.

1. Zadejte profil zařízení a vlastnosti sítě:

    | Parametr | Konfigurace |
    | ----------| ------------- |
    | **Hardwarový profil** | &lt;požadovaná architektura&gt; |
    | **Rozhraní pro správu** | **ens192** |
    | **Síťové parametry (poskytované zákazníkem)** | **IP adresa sítě pro správu:** <br/>**Maska podsítě:** <br/>**název hostitele zařízení:** <br/>**NÁZV** <br/>**Výchozí brána:** <br/>**Vstupní rozhraní:**|
    | **rozhraní mostu:** | Není nutné konfigurovat rozhraní mostu. Tato možnost je určena pouze pro zvláštní případy použití. |

1. Zadáním **Y** potvrďte nastavení.

1. Přihlašovací údaje pro přihlášení se generují automaticky a prezentují. Zkopírujte uživatelské jméno a heslo na bezpečném místě, protože jsou vyžadovány pro přihlášení a správu.

      - **Podpora**: administrativní uživatel pro správu uživatelů.

      - **CyberX**: ekvivalent kořenového adresáře pro přístup k zařízení.

1. Zařízení se restartuje.

1. Přístup ke konzole pro správu prostřednictvím dříve nakonfigurované IP adresy: `https://ip_address` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Snímek obrazovky, který zobrazuje přístup ke konzole pro správu.":::

## <a name="virtual-appliance-on-premises-management-console-installation"></a>Virtuální zařízení: instalace místní konzoly pro správu

Místní virtuální počítač konzoly pro správu podporuje následující architektury:

| Architektura | Specifikace | Využití | 
|--|--|--|
| Enterprise <br/>(Výchozí a nejběžnější) | PROCESOR: 8 <br/>Paměť: 32G RAM<br/> PEVNÝ DISK: 1,8 TB | Velká produkční prostředí | 
| Enterprise | PROCESOR: 4 <br/> Paměť: 8G RAM<br/> PEVNÝ DISK: 500 GB | Velká produkční prostředí |
| Enterprise | PROCESOR: 4 <br/>Paměť: 8G RAM <br/> PEVNÝ DISK: 100 GB | Malá testovací prostředí | 
   
### <a name="prerequisites"></a>Předpoklady

Místní Konzola pro správu podporuje možnosti nasazení VMware a Hyper-V. Než začnete s instalací, ověřte následující:

- VMware (ESXi 5,5 nebo novější) nebo hypervisor technologie Hyper-V (Windows 10 pro nebo Enterprise) je nainstalovaný a funkční.

- Hardwarové prostředky jsou k dispozici pro virtuální počítač.

- Máte instalační soubor ISO pro místní konzolu pro správu.
    
- Hypervisor je spuštěný.

### <a name="create-the-virtual-machine-esxi"></a>Vytvořit virtuální počítač (ESXi)

K vytvoření virtuálního počítače (ESXi):

1. Přihlaste se k ESXi, zvolte příslušné **úložiště dat** a pak vyberte **Prohlížeč úložiště dat**.

1. Nahrajte obrázek a vyberte **Zavřít**.

1. Přejít na **Virtual Machines**.

1. Vyberte **vytvořit/zaregistrovat virtuální počítač**.

1. Vyberte **vytvořit nový virtuální počítač** a vyberte **Další**.

1. Přidejte název snímače a vyberte:

   - Režim \<latest ESXi version>

   - Řada hostovaných operačních systémů: Linux

   - Verze operačního systému hosta: Ubuntu Linux (64 bitů)

1. Vyberte **Další**.

1. Zvolte příslušné úložiště dat a vyberte **Další**.

1. Změňte parametry virtuálního hardwaru podle požadované architektury.

1. V případě **jednotky CD/DVD 1** vyberte **soubor ISO úložiště dat** a vyberte soubor ISO, který jste nahráli dříve.

1. Vyberte **Další** > **Dokončit**.

### <a name="create-the-virtual-machine-hyper-v"></a>Vytvořit virtuální počítač (Hyper-V)

Vytvoření virtuálního počítače pomocí technologie Hyper-V:

1. Vytvořte virtuální disk ve Správci technologie Hyper-V.

1. Vyberte formát **VHDX**.

1. Vyberte **Další**.

1. Vyberte typ **dynamického rozšiřování**.

1. Vyberte **Další**.

1. Zadejte název a umístění virtuálního pevného disku.

1. Vyberte **Další**.

1. Zadejte požadovanou velikost (podle architektury).

1. Vyberte **Další**.

1. Zkontrolujte souhrn a vyberte **Dokončit**.

1. V nabídce **Akce** vytvořte nový virtuální počítač.

1. Vyberte **Další**.

1. Zadejte název virtuálního počítače.

1. Vyberte **Další**.

1. Vyberte **generaci** a nastavte ji na **1. generaci**.

1. Vyberte **Další**.

1. Zadejte přidělení paměti (podle architektury) a zaškrtněte políčko pro dynamickou paměť.

1. Vyberte **Další**.

1. Nakonfigurujte adaptér sítě podle síťové topologie serveru.

1. Vyberte **Další**.

1. Připojte k virtuálnímu počítači soubor VHDX, který jste předtím vytvořili.

1. Vyberte **Další**.

1. Zkontrolujte souhrn a vyberte **Dokončit**.

1. Pravým tlačítkem myši klikněte na nový virtuální počítač a pak vyberte **Nastavení**.

1. Vyberte **Přidat hardware** a přidejte nový adaptér pro **síťový adaptér**.

1. Pro **virtuální přepínač** vyberte přepínač, který se připojí k síti pro správu senzorů.

1. Přidělte prostředky procesoru (podle architektury).

1. Připojte bitovou kopii ISO konzoly pro správu k virtuální jednotce DVD.

1. Spusťte virtuální počítač.

1. V nabídce **Akce** vyberte **připojit** a pokračujte v instalaci softwaru.

### <a name="software-installation-esxi-and-hyper-v"></a>Instalace softwaru (ESXi a Hyper-V)

Spuštění virtuálního počítače spustí proces instalace z image ISO. Pro zvýšení zabezpečení můžete vytvořit druhé síťové rozhraní v místní konzole pro správu. Jedno síťové rozhraní je vyhrazeno pro vaše uživatele a může podporovat konfiguraci brány pro směrované sítě. Druhé síťové rozhraní je vyhrazeno pro všechny připojené senzory v rámci rozsahu IP adres.

Obě síťová rozhraní mají povolený uživatelský rozhraní (UI) a všechny funkce, které uživatelské rozhraní podporuje, budou k dispozici v sekundárním síťovém rozhraní, pokud není potřeba směrování. Vysoká dostupnost se spustí na sekundárním síťovém rozhraní.

Pokud se rozhodnete nenasadit sekundární síťové rozhraní, budou všechny funkce k dispozici prostřednictvím primárního síťového rozhraní. 

Postup instalace softwaru:

1. Vyberte **angličtinu**.

1. Vyberte požadovanou architekturu pro nasazení.

1. Definujte síťové rozhraní pro síť pro správu senzorů: rozhraní, IP adresu, podsíť, server DNS a výchozí bránu.

1. Volitelné Přidejte druhé síťové rozhraní do místní konzoly pro správu.

    1. `Please type sensor monitoring interface (Optional. Applicable when sensors are on a different network segment. For more information see the Installation instructions): <name of interface>`
    
    1. `Please type an IP address for the sensor monitoring interface (accessible by the sensors): <ip address>`
    
    1. `Please type a subnet mask for the sensor monitoring interface (accessible by the sensors): <subnet>`

1. Přihlašovací údaje pro přihlášení se generují automaticky a prezentují. Tyto přihlašovací údaje Uchovávejte na bezpečném místě, protože jsou nutné pro přihlášení a správu.

    | Uživatelské jméno | Description |
    |--|--|
    | Podpora | Administrativní uživatel pro správu uživatelů. |
    | CyberX | Ekvivalent kořene pro přístup k zařízení. |

1. Zařízení se restartuje.

1. Přístup ke konzole pro správu prostřednictvím dříve nakonfigurované IP adresy: `<https://ip_address>` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="Snímek obrazovky zobrazující přihlašovací obrazovku konzoly pro správu":::

## <a name="post-installation-validation"></a>Ověření po instalaci

Chcete-li ověřit instalaci fyzického zařízení, je třeba provést mnoho testů. Stejný proces ověřování platí pro všechny typy zařízení.

Proveďte ověření pomocí grafického uživatelského rozhraní nebo rozhraní příkazového řádku. Ověření je k dispozici pro **podporu** uživatelů a uživatele **CyberX**.

Ověření po instalaci musí zahrnovat následující testy:

  - **Správnosti test**: Ověřte, zda je systém spuštěn.

  - **Verze**: Ověřte správnost verze.

  - **ifconfig**: Ověřte, že jsou spuštěná všechna vstupní rozhraní nakonfigurovaná během procesu instalace.

### <a name="checking-system-health-by-using-the-gui"></a>Kontrola stavu systému pomocí grafického uživatelského rozhraní

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="Snímek obrazovky, který zobrazuje kontrolu stavu systému.":::

#### <a name="sanity"></a>Správnosti

- **Zařízení**: spustí kontrolu správnosti zařízení. Stejnou kontrolu můžete provést pomocí příkazu CLI `system-sanity` .

- **Verze**: Zobrazuje verzi zařízení.

- **Vlastnosti sítě**: zobrazí síťové parametry snímače.

#### <a name="redis"></a>Redis

- **Paměť**: poskytuje celkový přehled využití paměti, jako je třeba kolik paměti bylo využito a kolik zůstalo.

- **Nejdelší klíč**: zobrazuje nejdelší klíče, které by mohly způsobit velké využití paměti.

#### <a name="system"></a>Systém

- **Core log**: poskytuje poslední 500 řádky základního protokolu, které vám umožní zobrazit poslední řádky protokolu, aniž byste museli exportovat celý systémový protokol.

- **Správce úloh**: přeloží úlohy, které se zobrazují v tabulce procesů, do následujících vrstev: 
  
  - Trvalá vrstva (Redis) 
  - Peněžní vrstva (SQL)

- **Statistika sítě**: zobrazí statistiku sítě.

- **Top**: zobrazuje tabulku procesů. Jedná se o příkaz pro Linux, který poskytuje dynamický pohled na běžící systém v reálném čase.

- **Kontrola paměti zálohování**: poskytuje stav paměti zálohování a kontroluje následující:
  - Umístění záložní složky 
  - Velikost zálohovací složky
  - Omezení složky pro zálohování
  - Kdy došlo k poslední záloze
  - Velikost místa pro nadbytečné záložní soubory

- **ifconfig**: zobrazuje parametry pro fyzická rozhraní zařízení.

- **CyberX táhnout**: Zobrazuje síťový provoz a šířku pásma pomocí šesti sekund testů.

- **Chyby z jádra, log**: zobrazí chyby ze základního souboru protokolu.

Přístup k nástroji:

1. Přihlaste se ke senzoru pomocí přihlašovacích údajů uživatele **podpory** .

1. V okně **nastavení systému** vyberte **Statistika systému** .

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>Kontrola stavu systému pomocí rozhraní příkazového řádku

**Test 1: správnosti**

Ověřte, že je systém v provozu a je spuštěný:

1. Připojte se k rozhraní příkazového řádku pomocí terminálu pro Linux (například k disukázce) a **podpory** uživatelů.

1. Zadejte `system sanity`.

1. Ověřte, zda jsou všechny služby zelené (spuštěné).

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="Snímek obrazovky, který zobrazuje spuštěné služby.":::

1. Ověřte, že **je systém zapnutý. (prod)** Zobrazuje se v dolní části.

**Test 2: Kontrola verze**

Ověřte, že se používá správná verze:

1. Připojte se k rozhraní příkazového řádku pomocí terminálu pro Linux (například k disukázce) a **podpory** uživatelů.

1. Zadejte `system version`.

1. Ověřte, zda se zobrazí správná verze.

**Test 3: ověření sítě**

Ověřte, že jsou spuštěná všechna vstupní rozhraní nakonfigurovaná během procesu instalace:

1. Připojte se k rozhraní příkazového řádku pomocí terminálu pro Linux (například k disukázce) a **podpory** uživatelů.

1. Zadejte `network list` (ekvivalent příkazu pro Linux `ifconfig` ).

1. Ověřte, zda se zobrazí požadovaná vstupní rozhraní. Například pokud jsou nainstalovány dva čtyřjádrové síťové adaptéry Copper, v seznamu musí být 10 rozhraní.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="Snímek obrazovky, který zobrazuje seznam rozhraní.":::

**Test 4: přístup ke správě uživatelského rozhraní**

Ověřte, zda máte přístup k webovému uživatelskému rozhraní konzoly:

1. Připojte přenosný počítač k portu pro správu (**GB1**) pomocí kabelu Ethernet.

1. Zadejte adresu síťové karty přenosného počítače, která bude ve stejném rozsahu jako zařízení.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="Snímek obrazovky, který zobrazuje přístup pro správu k uživatelskému rozhraní.":::

1. Pomocí příkazů otestujte IP adresu zařízení z přenosného počítače, aby se ověřilo připojení (výchozí: 10.100.10.1).

1. Otevřete prohlížeč Chrome na přenosném počítači a zadejte IP adresu zařízení.

1. V okně **připojení není soukromé** , vyberte **Upřesnit** a pokračujte.

1. Test je úspěšný, když se zobrazí obrazovka přihlášení k programu Defender for IoT.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Snímek obrazovky zobrazující přístup ke konzole pro správu":::

## <a name="troubleshooting"></a>Poradce při potížích

### <a name="you-cant-connect-by-using-a-web-interface"></a>Nemůžete se připojit pomocí webového rozhraní.

1. Ověřte, že se počítač, ke kterému se pokoušíte připojit, nachází ve stejné síti jako zařízení.

1. Ověřte, zda je síť grafického uživatelského rozhraní připojena k portu pro správu.

1. Otestujte IP adresu zařízení. Pokud není k dispozici nástroj test:

   1. Připojte monitor a klávesnici k zařízení.

   1. K přihlášení použijte uživatele **podpory** a heslo.

   1. `network list`Aktuální IP adresu zobrazíte pomocí příkazu.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="Snímek obrazovky, který zobrazuje seznam sítí.":::

1. Pokud jsou parametry sítě nesprávně nakonfigurované, změňte je pomocí následujícího postupu:

   1. Použijte příkaz `network edit-settings` .

   1. Pokud chcete změnit IP adresu sítě pro správu, vyberte **Y**.

   1. Chcete-li změnit masku podsítě, vyberte **Y**.

   1. Pokud chcete změnit DNS, vyberte **Y**.

   1. Pokud chcete změnit výchozí IP adresu brány, vyberte **Y**.

   1. Pro změnu vstupního rozhraní (jenom senzor) vyberte **N**.

   1. Chcete-li použít nastavení, vyberte **Y**.

1. Po restartování se připojte pomocí přihlašovacích údajů uživatele podpory a pomocí `network list` příkazu ověřte, že se parametry změnily.

1. Zkuste znovu provést test a znovu se připojte z grafického uživatelského rozhraní.

### <a name="the-appliance-isnt-responding"></a>Zařízení nereaguje.

1. Připojte monitor a klávesnici k zařízení nebo použijte výstup pro vzdálené připojení k rozhraní příkazového řádku.

1. Přihlaste se pomocí přihlašovacích údajů uživatele **podpory** .

1. Použijte `system sanity` příkaz a ověřte, zda jsou spuštěné všechny procesy.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="Snímek obrazovky zobrazující příkaz System správnosti":::

V případě jakýchkoli dalších problémů kontaktujte [Podpora Microsoftu](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>Příloha A: port zrcadlení na virtuálním přepínači (ESXi)

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>Konfigurace portu SPAN na existujícím virtuálním přepínači

Virtuální přepínač nemá možnosti zrcadlení, k implementaci portu SPAN ale můžete použít alternativní řešení.

Konfigurace portu SPAN:

1. Otevřete vlastnosti virtuálního přepínače.

1. Vyberte **Přidat**.

1. Vyberte **virtuální počítač**  >  **Další**.

1. Vložte **síť** s popiskem sítě, vyberte položku **ID sítě VLAN**  >  **vše** a potom vyberte možnost **Další**.

1. Vyberte **Dokončit**.

1. Vyberte **rozsah sítě** > **Upravit*.

1. Vyberte **zabezpečení** a ověřte, že zásada **promiskuitního režimu** je nastavená na režim **přijetí** .

1. Vyberte **OK** a pak vyberte **Zavřít** a zavřete vlastnosti virtuálního přepínače.

1. Otevřete vlastnosti **virtuálního počítače XSense** .

1. V případě **síťového adaptéru 2** vyberte síť **rozsahu** .

1. Vyberte **OK**.

1. Připojte se ke senzoru a ověřte, že zrcadlení funguje.

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>Příloha B: snímače přístupu z místní konzoly pro správu

Zabezpečení systému můžete zlepšit tím, že zabráníte přímému přístupu uživatelů ke senzoru. Místo toho použijte tunelové propojení proxy serveru, které uživatelům umožní přístup ke senzoru z místní konzoly pro správu pomocí jediného pravidla brány firewall. Tento postup zužuje možnost neoprávněného přístupu k síťovému prostředí mimo senzor. Uživatelské prostředí, když se přihlašujete ke senzoru, zůstává stejné.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="Snímek obrazovky, který zobrazuje přístup ke senzoru.":::

Postup povolení tunelového propojení:

1. Přihlaste se k rozhraní příkazového řádku místní konzoly pro správu pomocí **CyberX** nebo **podpory** přihlašovacích údajů uživatele.

1. Zadejte `sudo cyberx-management-tunnel-enable`.

1. Stiskněte klávesu **Enter**.

1. Zadejte `--port 10000`.

### <a name="next-steps"></a>Další kroky

[Nastavení sítě](how-to-set-up-your-network.md)