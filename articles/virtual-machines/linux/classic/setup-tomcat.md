---
title: Nastavení Apache Tomcat na virtuálním počítači s Linuxem | Dokumentace Microsoftu
description: Zjistěte, jak nastavit Apache Tomcat7 pomocí Azure Virtual Machines s Linuxem.
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
ms.openlocfilehash: 5a5d052052be447ea2ccbd9231d3b03d38c7615c
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54266939"
---
# <a name="set-up-tomcat7-on-a-linux-virtual-machine-with-azure"></a>Instalace tomcat7 na virtuální počítač s Linuxem pomocí Azure
Apache Tomcat (nebo jednoduše Tomcat, také dříve se označovaly jako Jakarta Tomcat) je otevřít zdrojový webový server a servletový kontejner vyvinuté pomocí Apache Software Foundation (amp). Tomcat implementuje Java Servlet a JavaServer Pages (JSP) specifikace z Sun Microsystems. Tomcat poskytuje čistě Java HTTP prostředí webového serveru ke spouštění kódu v Javě. V nejjednodušší konfiguraci Tomcat běží v procesu jeden operační systém. Tento proces spouští Java virtual machine (JVM). Každý požadavek protokolu HTTP z prohlížeče na Tomcat je zpracován jako samostatného vlákna v procesu Tomcat.  

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manager a classic](../../../resource-manager-deployment-model.md). Tento článek popisuje, jak pomocí modelu nasazení classic. Doporučujeme, aby většina nových nasazení používala model Resource Manager. Použití šablony Resource Manageru k nasazení virtuálního počítače s Ubuntu s otevřít sadu JDK a Tomcat, naleznete v tématu [v tomto článku](https://azure.microsoft.com/documentation/templates/openjdk-tomcat-ubuntu-vm/).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

V tomto článku se bude instalace Tomcat7 na image Linuxu a nasaďte ji v Azure.  

Co se dozvíte:  

* Jak vytvořit virtuální počítač v Azure.
* Postup přípravy Tomcat7 virtuální počítač.
* Postup instalace Tomcat7.

Předpokládá se, že už máte předplatné Azure.  Pokud ne, můžete zaregistrovat k bezplatné zkušební verzi na [webu Azure](https://azure.microsoft.com/). Pokud máte předplatné MSDN, přečtěte si téma [speciální ceny Microsoft Azure: MSDN, MPN a BizSpark výhody](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Další informace o Azure najdete v tématu [co je Azure?](https://azure.microsoft.com/overview/what-is-azure/).

Tento článek předpokládá, že máte základní znalosti pracovních Tomcat a Linux.  

## <a name="phase-1-create-an-image"></a>Fáze 1: Vytvoření image
V této fázi vytvoříte virtuální počítač pomocí image Linuxu v Azure.  

### <a name="step-1-generate-an-ssh-authentication-key"></a>Krok 1: Generovat ověřovací klíč SSH
SSH je důležitý nástroj pro správce systému. Však není doporučeno konfigurace zabezpečení přístupu na základě hesla se určit lidské. Uživatelé se zlými úmysly může rozdělit na váš systém podle uživatelského jména a slabé heslo.

Dobrou zprávou je, že je způsob, jak zůstat otevřeno, vzdálený přístup a bez starostí o heslech. Tato metoda se skládá z ověřování pomocí asymetrického šifrování. Privátní klíč uživatele je ten, který uděluje ověřování. Můžete dokonce uzamknout uživatelský účet není povolit ověřování pomocí hesla.

Další výhodou této metody je, že není nutné odlišná hesla k přihlášení na různé servery. Můžete ověřovat pomocí osobní privátní klíč ve všech serverech, které brání si museli pamatovat více hesel.



Postupujte podle těchto kroků vygenerujete ověřovací klíč SSH.

1. Stáhněte a nainstalujte nástroje PuTTYgen z následujícího umístění: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Spusťte Puttygen.exe.
3. Klikněte na tlačítko **generovat** ke generování klíče. V procesu můžete zvýšit náhodnost přesunete ukazatel myši na prázdnou oblast v okně.  
   ![PuTTY Key Generator – snímek obrazovky, zobrazující tlačítko vygenerovat nové klíče][1]
4. Po dokončení procesu Generovat Puttygen.exe zobrazí nový veřejný klíč.  
   ![PuTTY Key Generator – snímek obrazovky, zobrazuje nový veřejný klíč a uložit privátní klíč tlačítko][2]
5. Vyberte a zkopírujte veřejný klíč a uložte ho do souboru s názvem publicKey.pem. Neklepejte **uložit veřejný klíč**, protože se liší od veřejného klíče chceme uložené veřejný klíč, formát souboru.
6. Klikněte na tlačítko **uložit privátní klíč**a uložte ho do souboru s názvem privateKey.ppk.

### <a name="step-2-create-the-image-in-the-azure-portal"></a>Krok 2: Vytvoření image na webu Azure Portal
1. V [portál](https://portal.azure.com/), klikněte na tlačítko **vytvořit prostředek** na hlavním panelu na vytvoření bitové kopie. Potom vyberte image Linuxu, který je podle svých potřeb. Následující příklad používá image Ubuntu 14.04.
![Snímek obrazovky portálu, který se zobrazí nové tlačítko][3]

2. Pro **název hostitele**, zadejte název pro adresu URL, kterou jste a internetoví klienti budou používat pro přístup k tomuto virtuálnímu počítači. Poslední část názvu DNS, třeba tomcatdemo definujte. Azure pak vygeneruje adresu URL jako tomcatdemo.cloudapp.net.  

3. Pro **SSH ověřovací klíč**, zkopírujte jeho hodnotu z publicKey.pem soubor, který obsahuje veřejný klíč vygenerovaný PuTTYgen.  
![Pole ověřovací klíč SSH na portálu][4]

4. Podle potřeby nakonfigurujte další nastavení a potom klikněte na tlačítko **vytvořit**.  

## <a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>Fáze 2: Příprava virtuálního počítače pro Tomcat7
V této fázi nakonfigurujte koncový bod pro provoz Tomcat a připojte se k novému virtuálnímu počítači.

### <a name="step-1-open-the-http-port-to-allow-web-access"></a>Krok 1: Otevřete port HTTP pro povolení webového přístupu
Koncové body v Azure se skládají z protokol TCP nebo UDP, také veřejné a privátní port. Privátní port je port, na kterém služba naslouchá na virtuálním počítači. Veřejný port je port, který Cloudová služba Azure naslouchá externě pro příchozí, internetový provoz.  

TCP port 8080 je výchozí číslo portu, který Tomcat používá k naslouchání. Pokud je tento port otevřít s koncový bod Azure, můžete a dalších internetoví klienti měli přístup k Tomcat stránky.  

1. Na portálu klikněte na tlačítko **Procházet** > **virtuálních počítačů**a potom klikněte na virtuální počítač, který jste vytvořili.  
   ![Snímek obrazovky s adresáři virtuálních počítačů][5]
2. K virtuálnímu počítači přidat koncový bod, klikněte na tlačítko **koncové body** pole.
   ![Snímek obrazovky zobrazující pole koncových bodů][6]
3. Klikněte na tlačítko **Add** (Přidat).  

   1. Pro koncový bod, zadejte název pro koncový bod **koncový bod**a pak zadejte 80 **veřejný Port**.  

      Pokud je nastavený na 80, není nutné zahrnovat číslo portu v adrese URL, který se používá pro přístup k Tomcat. Například, http://tomcatdemo.cloudapp.net.    

      Pokud je nastavena na jinou hodnotu, jako je například 81, budete muset přidat číslo portu k adrese URL pro přístup k Tomcat. Například http://tomcatdemo.cloudapp.net:81/.
   2. Zadejte 8080 v **privátní Port**. Ve výchozím nastavení Tomcat naslouchá na TCP port 8080. Pokud jste změnili výchozí naslouchání portu Tomcat, měli byste aktualizovat **privátní Port** být stejné jako Tomcat port pro naslouchání.  
      ![Snímek obrazovky z uživatelského rozhraní, který zobrazuje přidat příkaz veřejný Port a privátní Port][7]
4. Klikněte na tlačítko **OK** ke svému virtuálnímu počítači přidat koncový bod.

### <a name="step-2-connect-to-the-image-you-created"></a>Krok 2: Připojte se k image, kterou jste vytvořili
Můžete použít jakýkoli nástroj SSH pro připojení k virtuálnímu počítači. V tomto příkladu používáme PuTTY.  

1. Získejte název DNS virtuálního počítače z portálu.
    1. Klikněte na tlačítko **Procházet** > **virtuálních počítačů**.
    2. Vyberte název vašeho virtuálního počítače a potom klikněte na tlačítko **vlastnosti**.
    3. V **vlastnosti** dlaždici, podívejte se **název domény** pole a získat tak název DNS.  

2. Získat číslo portu pro připojení SSH z **SSH** pole.  
![Snímek obrazovky zobrazující číslo portu pro připojení SSH][8]

3. Stáhněte si [PuTTY](http://www.putty.org/).  

4. Po stažení, klikněte na spustitelný soubor Putty.exe. V konfigurace PuTTY základní možnosti konfigurace s názvem hostitele a portu číslo, které se získává z vlastnosti virtuálního počítače.   
![Snímek obrazovky, který ukazuje možnosti názvu a portu hostitele konfigurace PuTTY][9]

5. V levém podokně klikněte na tlačítko **připojení** > **SSH** > **Auth**a potom klikněte na tlačítko **Procházet** zadat umístění souboru privateKey.ppk. Obsahuje privátní klíč, který je generován PuTTYgen dříve v souboru privateKey.ppk "fáze 1: Vytvoření image"části tohoto článku.  
![Snímek obrazovky zobrazující hierarchii adresářů připojení a tlačítko pro procházení][10]

6. Klikněte na **Otevřít**. Upozorní může být okno se zprávou. Pokud jste nakonfigurovali DNS název a číslo portu správně, klikněte na tlačítko **Ano**.
![Snímek obrazovky zobrazující oznámení][11]

7. Zobrazí se výzva k zadání vašeho uživatelského jména.  
![Snímek obrazovky, který ukazuje, kam je třeba zadat uživatelské jméno][12]

8. Zadejte uživatelské jméno, které jste použili k vytvoření virtuálního počítače "fáze 1: Vytvoření image"části výše v tomto článku. Zobrazí se přibližně takto:  
![Snímek obrazovky zobrazující potvrzení ověřování][13]

## <a name="phase-3-install-software"></a>Fáze 3: Instalace softwaru
V této fázi instalace prostředí Java runtime, Tomcat7 a další komponenty Tomcat7.  

### <a name="java-runtime-environment"></a>Běhové prostředí Java
Tomcat je napsána v jazyce Java. Zobrazit [Azure nepodporuje JDK](https://aka.ms/azure-jdks) informace o tom, jak získat plně podporované moduly runtime Java. Můžete také přinést vlastní, ale zbývající část tohoto článku bude používat verzích podporovaných systémem Azure.


#### <a name="install-azure-supported-jdk"></a>Instalace Azure nepodporuje JDK

Postupujte podle `apt-get` popsané na pokyny pro instalaci [Azul Zulu Enterprise pro Azure](https://www.azul.com/downloads/azure-only/zulu/#apt-repo) webu.

#### <a name="confirm-that-java-installation-is-successful"></a>Potvrďte, jestli se úspěšně dokončila instalaci Javy
Pokud chcete otestovat, jestli je správně nainstalované prostředí Java runtime můžete použít příkaz podobný tomuto:  
    Java – verze  

Zobrazí zpráva podobná následující: ![Úspěšná instalace zpráva OpenJDK][14]


### <a name="install-tomcat7"></a>Instalace Tomcat7
Instalace Tomcat7 použijte následující příkaz.  

    sudo apt-get install tomcat7  

Pokud nepoužíváte Tomcat7, použijte jeho odpovídající variantu tento příkaz.  

#### <a name="confirm-that-tomcat7-installation-is-successful"></a>Potvrďte, že je instalace Tomcat7 úspěšná
Zkontrolujte, zda Tomcat7 je úspěšně nainstalována, přejděte na název DNS serveru Tomcat. V tomto článku najdete příklad adresy URL je http://tomcatexample.cloudapp.net/. Pokud se zobrazí zpráva podobná následující, Tomcat7 správně nainstalován.
![Úspěšné zprávy instalace Tomcat7][16]

### <a name="install-other-tomcat7-components"></a>Instalace součásti Tomcat7
Existují další volitelné komponenty Tomcat, které můžete nainstalovat.  

Použití **sudo hledat apt-cache tomcat7** příkazu zobrazte všechny dostupné komponenty. Následující příkazy použijte k instalaci některých komponent užitečné.  

    sudo apt-get install tomcat7-admin      #admin web applications

    sudo apt-get install tomcat7-user         #tools to create user instances  

## <a name="phase-4-configure-tomcat7"></a>Fáze 4: Konfigurace Tomcat7
V této fázi budete spravovat Tomcat.

### <a name="start-and-stop-tomcat7"></a>Spuštění a zastavení Tomcat7
Tomcat7 server automaticky spustí, když ho nainstalujete. Můžete ho spustit také pomocí následujícího příkazu:   

    sudo /etc/init.d/tomcat7 start

Zastavení Tomcat7:

    sudo /etc/init.d/tomcat7 stop

Chcete-li zobrazit stav Tomcat7:

    sudo /etc/init.d/tomcat7 status

Restartování služeb Tomcat: 

    sudo /etc/init.d/tomcat7 restart

### <a name="tomcat7-administration"></a>Správa Tomcat7
Můžete upravit konfigurační soubor uživatele Tomcat nastavit přihlašovací údaje správce. Použijte následující příkaz:  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Zde naleznete příklad:  
![Snímek obrazovky zobrazující výstup příkazů sudo vi][17]  

> [!NOTE]
> Vytvořte silné heslo pro uživatelské jméno správce.  

Po úpravě tohoto souboru, musíte restartovat Tomcat7 služeb pomocí následujícího příkazu zkontrolujte, že se změny projeví:  

    sudo /etc/init.d/tomcat7 restart  

Otevřete prohlížeč a zadejte **http://<your tomcat server DNS name>/správce/html** jako adresu URL. Například v tomto článku, adresa URL je http://tomcatexample.cloudapp.net/manager/html.  

Po připojení by měl vypadat nějak takto:  
![Snímek obrazovky Správce aplikací webové Tomcat][18]

## <a name="common-issues"></a>Běžné problémy
### <a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Nedaří se virtuální počítač s Tomcat a Moodle z Internetu
#### <a name="symptom"></a>Příznak  
  Tomcat je spuštěná, ale nemůžete zobrazit Tomcat výchozí stránku v prohlížeči.
#### <a name="possible-root-cause"></a>Možnou hlavní příčinou   

  * Port pro naslouchání Tomcat není stejný jako privátní port koncového bodu virtuálního počítače pro provoz Tomcat.  

     Zkontrolujte veřejný port a privátní port nastavení koncového bodu a ujistěte se, že privátní port je že stejné jako Tomcat port pro naslouchání. Viz "fáze 1: Vytvoření image"části tohoto článku Další pokyny ke konfiguraci koncových bodů pro virtuální počítač.  

     Ke zjištění portu naslouchání Tomcat, otevřete /etc/httpd/conf/httpd.conf (verze Red Hat) nebo /etc/tomcat7/server.xml (Debian vydaná verze). Výchozí port pro naslouchání Tomcat je 8080. Zde naleznete příklad:  

        <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"   URIEncoding="UTF-8"            redirectPort="8443" />  

     Pokud používáte virtuální počítač jako Debian nebo Ubuntu a chcete změnit, výchozí port z Tomcat naslouchání (například 8081), měli byste taky otevřít port pro operační systém. Nejprve otevřete profil:  

        sudo vi /etc/default/tomcat7  

     Pak zrušte komentář na posledním řádku a změňte "žádný" na "Ano".  

        AUTHBIND=yes
  2. Brána firewall zakázal naslouchání portu Tomcat.

     Zobrazí se pouze na výchozí stránce Tomcat z místního hostitele. Problém je velmi pravděpodobné, že port, který je sleduje Tomcat, je blokován branou firewall. Můžete použít nástroj w3m a přejděte na webovou stránku. Následující příkazy instalace w3m a přejděte na stránku výchozího Tomcat:  


        sudo yum install w3m w3m-img


        w3m http://localhost:8080  
#### <a name="solution"></a>Řešení

  * Pokud Tomcat naslouchání port není stejný jako privátní port koncového bodu pro provoz do virtuálního počítače, potřebujete změnit privátní port bude, že stejná jako Tomcatu port pro naslouchání.   
  2. Je-li tento problém je brána firewall/iptables, přidejte následující řádky do /etc/sysconfig/iptables. Druhý řádek je potřeba jenom pro komunikaci přes protokol https:  

      -A -p tcp -m tcp – dport 80 -j přijmout vstup

      -A -p tcp -m tcp – dport 443 -j přijmout vstup  

     > [!IMPORTANT]
     > Ujistěte se, že předchozí řádky jsou umístěny nad všechny řádky, které by globálně omezení přístupu, jako je následující:-INPUT -j ODMÍTNOUT--odmítnout – s icmp hostitele zakázáno



Chcete-li znovu načíst iptables, spusťte následující příkaz:

    service iptables restart

To jsme otestovali na CentOS 6.3.

### <a name="permission-denied-when-you-upload-project-files-to-varlibtomcat7webapps"></a>Oprávnění byla odepřena. když nahrajete soubory projektu do /var/lib/tomcat7/webapps /
#### <a name="symptom"></a>Příznak
  Při použití klientem SFTP (například Filezilly) můžete připojit k virtuálnímu počítači a přejít na /var/lib/tomcat7/webapps/publikování webu, zobrazí chybová zpráva podobná následující:  

     status:    Listing directory /var/lib/tomcat7/webapps
     Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
     Error:    /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
     Error:    File transfer failed
#### <a name="possible-root-cause"></a>Možnou hlavní příčinou
  Nemáte oprávnění pro přístup ke složce /var/lib/tomcat7/webapps.  
#### <a name="solution"></a>Řešení  
  Je nutné získat oprávnění z kořenového účtu. Vlastnictví této složky můžete změnit z kořenového adresáře na uživatelské jméno, které jste použili při zřízení počítače. Tady je příklad s názvem účtu azureuser:  

     sudo chown azureuser -R /var/lib/tomcat7/webapps

  Použijte parametr -R příliš použít oprávnění pro všechny soubory v adresáři.  

  Tento příkaz funguje taky pro adresáře. Možnost -R se změní oprávnění pro všechny soubory a adresáře v adresáři. Zde naleznete příklad:  

     sudo chown -R username:group directory  

  Tento příkaz změní vlastnictví (uživatele a skupiny) pro všechny soubory a adresáře, které jsou v adresáři.  

  Následující příkaz změní pouze oprávnění k adresáři složky. Soubory a složky do adresáře se nezmění.  

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
