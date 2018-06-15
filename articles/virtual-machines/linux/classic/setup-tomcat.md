---
title: Nastavit Apache Tomcat na virtuální počítač s Linuxem | Microsoft Docs
description: Zjistěte, jak nastavit Apache Tomcat7 pomocí virtuální počítače Azure s Linuxem.
services: virtual-machines-linux
documentationcenter: ''
author: NingKuang
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 45ecc89c-1cb0-4e80-8944-bd0d0bbedfdc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: ningk
ms.openlocfilehash: 161a56a019f8c2c8ce5e3890e73ad5c5710e7b82
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841611"
---
# <a name="set-up-tomcat7-on-a-linux-virtual-machine-with-azure"></a>Nastavit Tomcat7 na virtuální počítač s Linuxem v Azure
Apache Tomcat (nebo jednoduše Tomcat, také dříve se označovaly jako Jakarta Tomcat) je webový server s otevřeným zdrojem a kontejner servlet vyvinuté pomocí softwaru Foundation Apache (amp). Tomcat implementuje Java Servlet a specifikace JavaServer stránky (JSP) z Sun Microsystems. Tomcat poskytuje čistý Java HTTP prostředí webového serveru ke spouštění kódu v jazyce Java. V nejjednodušší konfiguraci Tomcat běží v procesu jednoho operačního systému. Tento proces se spustí nástroje Java virtual machine (JVM). Každý požadavek HTTP z prohlížeče do Tomcat zpracovávány jako samostatné vláken v procesu Tomcat.  

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek popisuje postup použití modelu nasazení classic. Doporučujeme vám, že většina nových nasazení používala model Resource Manager. Použití šablony Resource Manageru k nasazení virtuálního počítače s Ubuntu s otevřete JDK a Tomcat, najdete v části [v tomto článku](https://azure.microsoft.com/documentation/templates/openjdk-tomcat-ubuntu-vm/).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

V tomto článku bude instalace Tomcat7 na bitovou kopii systému Linux a nasadit v Azure.  

Co se dozvíte:  

* Postup vytvoření virtuálního počítače v Azure.
* Postup přípravy Tomcat7 virtuálního počítače.
* Postup instalace Tomcat7.

Předpokládá se, že už máte předplatné Azure.  Pokud není, můžete si zaregistrovat bezplatnou zkušební verzi na [webu Azure](https://azure.microsoft.com/). Pokud máte předplatné MSDN, najdete v části [Microsoft Azure speciální ceny: MSDN, MPN a výhody BizSpark](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Další informace o Azure najdete v tématu [co je Azure?](https://azure.microsoft.com/overview/what-is-azure/).

Tento článek předpokládá, že máte základní znalosti práce Tomcat a Linux.  

## <a name="phase-1-create-an-image"></a>Fáze 1: Vytvoření image
V této fázi vytvoříte virtuální počítač pomocí bitové kopie systému Linux v Azure.  

### <a name="step-1-generate-an-ssh-authentication-key"></a>Krok 1: Generovat ověřovací klíč SSH
SSH je důležité nástroj pro správce systému. Však není doporučeno konfigurace zabezpečení přístupu na základě určit lidské hesla. Uživatelé se zlými úmysly může rozdělit na váš systém podle uživatelského jména a vytváření silných hesel.

Dobrá zpráva je, že je způsob, jak nechte vzdálený přístup otevřené a nestarat se o hesla. Tato metoda se skládá z ověřování s asymetrické šifrování. Privátní klíč uživatele je ten, který uděluje ověřování. Můžete dokonce uzamčení uživatelského účtu nepovolíte ověřování hesla.

Další výhodou této metody je nepotřebujete různá hesla k přihlášení na jiné servery. Můžete ověřovat pomocí osobní privátní klíče ve všech serverech, což zabraňuje si museli pamatovat více hesel.



Postupujte podle těchto kroků generovat ověřovací klíč SSH.

1. Stáhněte a nainstalujte PuTTYgen z následujícího umístění: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Spusťte Puttygen.exe.
3. Klikněte na tlačítko **generování** ke generování klíče. V procesu můžete zvýšit náhodnost přesunutím myši nad prázdné místo v okně.  
   ![PuTTY snímek obrazovky generátor klíč, který ukazuje tlačítko vygenerovat nový klíč][1]
4. Po dokončení procesu generování Puttygen.exe zobrazí nový veřejný klíč.  
   ![PuTTY snímek obrazovky generátor klíč, který ukazuje nový veřejný klíč a uložení privátního klíče tlačítko][2]
5. Vyberte a zkopírujte veřejný klíč a uložit ho do souboru s názvem publicKey.pem. Nemáte klikněte na tlačítko **uložit veřejný klíč**, protože formát souboru uložené veřejný klíč se liší od chceme veřejný klíč.
6. Klikněte na tlačítko **uložit privátní klíč**a uložte ho do souboru s názvem privateKey.ppk.

### <a name="step-2-create-the-image-in-the-azure-portal"></a>Krok 2: Vytvoření bitové kopie na portálu Azure
1. V [portál](https://portal.azure.com/), klikněte na tlačítko **vytvořit prostředek** na hlavním panelu na vytvoření bitové kopie. Zvolte Linux bitovou kopii, která je založena na vašich potřebách. Následující příklad používá bitovou kopii Ubuntu 14.04.
![Snímek obrazovky portálu, který ukazuje tlačítka Nová][3]

2. Pro **název hostitele**, zadejte název pro adresu URL, kterou jste a internetové klienty se bude používat pro přístup k tomuto virtuálnímu počítači. Zadejte poslední část názvu DNS, například tomcatdemo. Azure pak vygeneruje adresu URL jako tomcatdemo.cloudapp.net.  

3. Pro **SSH ověřovací klíč**, zkopírujte hodnotu klíče ze souboru publicKey.pem, který obsahuje veřejný klíč generované PuTTYgen.  
![Pole ověřovací klíč SSH na portálu][4]

4. Podle potřeby nakonfigurujte další nastavení a potom klikněte na **vytvořit**.  

## <a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>Fáze 2: Příprava virtuálního počítače pro Tomcat7
V této fázi konfigurace koncového bodu pro provoz Tomcat a potom se připojte k nového virtuálního počítače.

### <a name="step-1-open-the-http-port-to-allow-web-access"></a>Krok 1: Otevřete port HTTP pro povolení webového přístupu
Protokol TCP nebo UDP, společně s veřejné a privátní port obsahovat koncové body v Azure. Privátní port je port, který služba naslouchá na virtuálním počítači. Veřejný port je port, který cloudovou službu systému Azure naslouchá externě pro příchozí internetového provozu.  

TCP port 8080 je výchozí číslo portu, který Tomcat používá k naslouchání. Tento port se při otevření s koncový bod Azure, můžete a dalších internetoví klienti mohou přistupovat Tomcat stránky.  

1. Na portálu, klikněte na tlačítko **Procházet** > **virtuální počítače**a potom klikněte na virtuální počítač, který jste vytvořili.  
   ![Snímek obrazovky adresáři virtuální počítače][5]
2. Chcete-li přidat koncový bod virtuálního počítače, klikněte na tlačítko **koncové body** pole.
   ![Snímek obrazovky zobrazující pole koncových bodů][6]
3. Klikněte na tlačítko **Add** (Přidat).  

   1. Pro koncový bod, zadejte název koncového bodu v **koncový bod**a pak zadejte 80 v **veřejný Port**.  

      Pokud je nastavena na 80, nemusíte zahrnovat číslo portu v adrese URL, který se používá pro přístup k Tomcat. Například, http://tomcatdemo.cloudapp.net.    

      Pokud je nastavena na jinou hodnotu, jako je například 81, budete muset přidat číslo portu na adresu URL pro přístup k Tomcat. Například http://tomcatdemo.cloudapp.net:81/.
   2. Zadejte 8080 v **privátní Port**. Ve výchozím nastavení Tomcat naslouchá na portu TCP 8080. Pokud jste změnili výchozí naslouchání portu Tomcat, by měl aktualizovat **privátní Port** být stejné jako Tomcat port pro naslouchání.  
      ![Snímek obrazovky z uživatelské rozhraní, které se zobrazuje příkaz přidat, veřejný Port a privátní Port][7]
4. Klikněte na tlačítko **OK** přidat koncový bod k virtuálnímu počítači.

### <a name="step-2-connect-to-the-image-you-created"></a>Krok 2: Připojení na bitovou kopii, kterou jste vytvořili
Můžete vybrat jakýkoli SSH nástroj pro připojení k virtuálnímu počítači. V tomto příkladu používáme PuTTY.  

1. Získáte název DNS virtuálního počítače z portálu.
    1. Klikněte na tlačítko **Procházet** > **virtuální počítače**.
    2. Vyberte název virtuálního počítače a pak klikněte na tlačítko **vlastnosti**.
    3. V **vlastnosti** dlaždici, podívejte se **název domény** pole získat název DNS.  

2. Získat číslo portu pro SSH připojení z **SSH** pole.  
![Snímek obrazovky, který se zobrazuje číslo portu SSH připojení][8]

3. Stáhněte si [PuTTY](http://www.putty.org/).  

4. Po stažení, klikněte na spustitelný soubor Putty.exe. V konfiguraci PuTTY nakonfigurujte základní možnosti s názvem hostitele a portu číslo, které se získávají z vlastností virtuálního počítače.   
![Snímek obrazovky, který ukazuje možnosti název a port hostitele PuTTY konfigurace][9]

5. V levém podokně klikněte na **připojení** > **SSH** > **Auth**a potom klikněte na **Procházet** k určení umístění souboru privateKey.ppk. Soubor privateKey.ppk obsahuje privátní klíč, který je generovaný PuTTYgen dříve v "fáze 1: vytvoření image" tohoto článku.  
![Snímek obrazovky, který se zobrazuje hierarchie adresářů připojení a tlačítko Procházet][10]

6. Klikněte na tlačítko **otevřete**. Může být upozorněni podle okno se zprávou. Pokud jste nakonfigurovali název DNS a číslo portu správně, klikněte na tlačítko **Ano**.
![Snímek obrazovky, který se zobrazí oznámení.][11]

7. Zobrazí se výzva k zadání vašeho uživatelského jména.  
![Snímek obrazovky, který ukazuje, kde k zadání uživatelského jména][12]

8. Zadejte uživatelské jméno, které jste použili k vytvoření virtuálního počítače "fáze 1: vytvoření image" dříve v tomto článku. Zobrazí se přibližně takto:  
![Snímek obrazovky zobrazující potvrzení ověřování][13]

## <a name="phase-3-install-software"></a>Fáze 3: Instalace softwaru
V této fázi nainstalujte prostředí Java runtime, Tomcat7 a další součásti Tomcat7.  

### <a name="java-runtime-environment"></a>Prostředí Java runtime
Tomcat je napsán v jazyce Java. Existují dva typy sad Java Development Kit (JDKs), OpenJDK a Oracle JDK. Můžete zvolit ten, který chcete.  

> [!NOTE]
> Obě JDKs mít téměř stejný kód pro třídy v rozhraní API Java, ale kód pro virtuální počítač se liší. OpenJDK obvykle používat otevřené knihovny, Oracle JDK obvykle pro použití uzavřené portů. Oracle JDK má více tříd a některé vyřešili chyb a Oracle JDK je stabilnější než OpenJDK.

#### <a name="install-openjdk"></a>Nainstalujte OpenJDK  

Chcete-li stáhnout OpenJDK použijte následující příkaz.   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  


* Chcete-li vytvořit adresář, který bude obsahovat soubory JDK:  

        sudo mkdir /usr/lib/jvm  
* Extrahujte soubory JDK do adresáře/usr/lib/jvm /:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/

#### <a name="install-oracle-jdk"></a>Nainstalujte Oracle JDK


Použijte následující příkaz ke stažení z webu Oracle Oracle JDK.  

     wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  
* Chcete-li vytvořit adresář, který bude obsahovat soubory JDK:  

        sudo mkdir /usr/lib/jvm  
* Extrahujte soubory JDK do adresáře/usr/lib/jvm /:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  
* Chcete-li nastavit Oracle JDK jako virtuální počítač Java výchozí:  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  

        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

#### <a name="confirm-that-java-installation-is-successful"></a>Potvrďte, že Java instalace byla úspěšně dokončena
Příkaz takto můžete otestovat, pokud je prostředí Java runtime správně nainstalován:  

    java -version  

Pokud jste nainstalovali OpenJDK, zobrazí se zpráva podobná následující: ![OpenJDK úspěšné instalace zpráv][14]

Pokud jste nainstalovali Oracle JDK, zobrazí se zpráva podobná následující: ![zpráva instalace úspěšná JDK Oracle][15]

### <a name="install-tomcat7"></a>Instalace Tomcat7
Použijte následující příkaz k instalaci Tomcat7.  

    sudo apt-get install tomcat7  

Pokud nepoužíváte Tomcat7, použijte jeho odpovídající variantu tohoto příkazu.  

#### <a name="confirm-that-tomcat7-installation-is-successful"></a>Potvrďte, že je instalace Tomcat7 úspěšné
Kontrola, pokud je úspěšně nainstalována Tomcat7, vyhledejte název DNS Tomcat server. V tomto článku je příklad adresy URL http://tomcatexample.cloudapp.net/. Pokud se zobrazí zpráva podobná následující, Tomcat7 je správně nainstalován.
![Zpráva úspěšné instalace Tomcat7][16]

### <a name="install-other-tomcat7-components"></a>Instalace součásti Tomcat7
Existují další volitelné součásti Tomcat, které můžete nainstalovat.  

Použití **sudo výstižný mezipaměti vyhledávání tomcat7** příkazu zobrazte všechny dostupné součásti. Použijte následující příkazy pro instalaci některé užitečné součásti.  

    sudo apt-get install tomcat7-admin      #admin web applications

    sudo apt-get install tomcat7-user         #tools to create user instances  

## <a name="phase-4-configure-tomcat7"></a>Fáze 4: Konfigurace Tomcat7
V této fázi spravovat Tomcat.

### <a name="start-and-stop-tomcat7"></a>Spuštění a zastavení Tomcat7
Tomcat7 server se automaticky spustí, když ho nainstalujete. Můžete také spustit pomocí následujícího příkazu:   

    sudo /etc/init.d/tomcat7 start

Chcete-li zastavit Tomcat7:

    sudo /etc/init.d/tomcat7 stop

Chcete-li zobrazit stav Tomcat7:

    sudo /etc/init.d/tomcat7 status

K restartu služeb Tomcat: 

    sudo /etc/init.d/tomcat7 restart

### <a name="tomcat7-administration"></a>Správa Tomcat7
Můžete upravit konfigurační soubor uživatele Tomcat k nastavení přihlašovacích údajů správce. Použijte následující příkaz:  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Zde naleznete příklad:  
![Snímek obrazovky zobrazující výstupu příkazu sudo vi][17]  

> [!NOTE]
> Vytvořte silné heslo pro uživatelské jméno správce.  

Po úpravě tohoto souboru, musíte restartovat Tomcat7 službám pomocí následujícího příkazu zkontrolujte, že tyto změny začnou platit:  

    sudo /etc/init.d/tomcat7 restart  

Otevřete prohlížeč a zadejte **http://<your tomcat server DNS name>/manager/html** jako adresu URL. Například v tomto článku, adresa URL je http://tomcatexample.cloudapp.net/manager/html.  

Po připojení, měli byste vidět něco podobného jako následující:  
![Snímek obrazovky Správce aplikací webové Tomcat][18]

## <a name="common-issues"></a>Běžné problémy
### <a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Nelze získat přístup k virtuálnímu počítači s Tomcat a Moodle z Internetu
#### <a name="symptom"></a>Příznaky  
  Tomcat běží, ale nemůžete zobrazit Tomcat výchozí stránku v prohlížeči.
#### <a name="possible-root-cause"></a>Možné příčiny   

  * Port pro naslouchání Tomcat není stejný jako privátní port koncový bod virtuálního počítače pro provoz Tomcat.  

     Zkontrolujte veřejný port a privátní port koncového bodu nastavení a ujistěte se, že privátní port je že stejný jako Tomcat port pro naslouchání. Najdete v části "fáze 1: vytvoření image" tohoto článku Pokyny ke konfiguraci koncových bodů pro virtuální počítač.  

     Ke zjištění portu naslouchání Tomcat, otevřete /etc/httpd/conf/httpd.conf (Red Hat vydání) nebo /etc/tomcat7/server.xml (Debian vydání). Ve výchozím nastavení je port pro naslouchání Tomcat 8080. Zde naleznete příklad:  

        <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"   URIEncoding="UTF-8"            redirectPort="8443" />  

     Pokud používáte virtuální počítač jako Debian a Ubuntu a chcete změnit, výchozí port z Tomcat naslouchání (například 8081), měli byste taky otevřít port pro operační systém. První otevřete profil:  

        sudo vi /etc/default/tomcat7  

     Potom Odkomentujte poslední řádek a změňte "žádný" "Ano".  

        AUTHBIND=yes
  2. Brána firewall zakázal naslouchání portu Tomcat.

     Zobrazí se pouze výchozí stránka Tomcat z místního hostitele. Problém je velmi pravděpodobné, že je port, který je sleduje Tomcat, blokován branou firewall. Můžete použít nástroj w3m a přejděte na webovou stránku. Následující příkazy instalace w3m a přejděte na stránku výchozího Tomcat:  


        sudo yum instalace w3m w3m-img


        w3m http://localhost:8080  
#### <a name="solution"></a>Řešení

  * Pokud naslouchat Tomcat port není stejný jako privátní port koncového bodu pro provoz do virtuálního počítače, je nutné změnit privátní port, který má být že stejné jako Tomcat port pro naslouchání.   
  2. Pokud tento problém je způsoben brány firewall nebo iptables, přidejte následující řádky do /etc/sysconfig/iptables. Druhý řádek je potřeba jenom pro provoz https:  

      -A -p tcp -m tcp – dport 80 -j přijmout vstup

      -A -p tcp -m tcp – dport 443 -j přijmout vstup  

     > [!IMPORTANT]
     > Zajistěte, aby předchozí řádky jsou umístěny nad všechny řádky, které by globálně omezení přístupu, jako jsou následující: - A -j ODMÍTNĚTE – odmítněte with icmp hostitele zakázáno vstup



Chcete-li znovu načíst iptables, spusťte následující příkaz:

    service iptables restart

To byl otestován v CentOS 6.3.

### <a name="permission-denied-when-you-upload-project-files-to-varlibtomcat7webapps"></a>Oprávnění byla odepřena při nahrávání souborů projektu do /var/lib/tomcat7/webapps /
#### <a name="symptom"></a>Příznaky
  Použijete-li pro připojení k virtuálnímu počítači a přejděte do /var/lib/tomcat7/webapps/publikování webu klientem SFTP (například FileZilla), můžete získat chybová zpráva podobná této:  

     status:    Listing directory /var/lib/tomcat7/webapps
     Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
     Error:    /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
     Error:    File transfer failed
#### <a name="possible-root-cause"></a>Možné příčiny
  Nemáte oprávnění k přístupu ke složce /var/lib/tomcat7/webapps.  
#### <a name="solution"></a>Řešení  
  Musíte získat oprávnění z kořenového účtu. Můžete změnit vlastnictví této složky z kořenového adresáře na uživatelské jméno, které jste použili při zřizování na počítač. Tady je příklad s názvem účtu azureuser:  

     sudo chown azureuser -R /var/lib/tomcat7/webapps

  Použijte parametr -R příliš použít oprávnění pro všechny soubory v adresáři.  

  Tento příkaz lze použít také pro adresáře. -R možnost změny oprávnění pro všechny soubory a adresáře v adresáři. Zde naleznete příklad:  

     sudo chown -R username:group directory  

  Tento příkaz změní vlastnictví (uživatele a skupiny) pro všechny soubory a adresáře, které jsou v adresáři.  

  Tento příkaz změní jenom oprávnění adresáři složky. Soubory a složky v adresáři, nebudou změněny.  

     sudo chown username:group directory

[1]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png
