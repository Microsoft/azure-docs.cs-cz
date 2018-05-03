Postupujte podle těchto kroků k instalaci a spuštění MongoDB ve virtuálním počítači s Windows serverem.

> [!IMPORTANT]
> Funkce zabezpečení MongoDB, jako je ověřování a vazbu IP adresy, nejsou povolené ve výchozím nastavení. Před nasazením MongoDB v produkčním prostředí by měl povolit funkce zabezpečení.  Další informace najdete v tématu [zabezpečení a ověřování](http://www.mongodb.org/display/DOCS/Security+and+Authentication).
>
>

1. Po připojení k virtuálnímu počítači pomocí vzdálené plochy, otevřete Internet Explorer z **spustit** nabídky na virtuálním počítači.
2. Vyberte **nástroje** tlačítko v pravém horním rohu.  V **Možnosti Internetu**, vyberte **zabezpečení** a potom vyberte **Důvěryhodné servery** ikonu a nakonec klikněte **lokality** tlačítko. Přidat *https://\*. mongodb.org* do seznamu důvěryhodných serverů.
3. Přejděte na [stáhne - MongoDB](https://www.mongodb.com/download-center#community).
4. Najít **aktuální stabilní verzi** z **komunity serveru**, vyberte nejnovější **64-bit** verze ve sloupci systému Windows. Stáhněte a spusťte instalační program MSI.
5. MongoDB je obvykle nainstalován v C:\Program Files\MongoDB. Vyhledejte proměnné prostředí na ploše a přidat cestu binární soubory MongoDB do proměnné PATH. Například může najít binární soubory na C:\Program Files\MongoDB\Server\3.4\bin na váš počítač.
6. Vytváření adresářů MongoDB protokolu a data na disku data (například disk **F:**) jste vytvořili v předchozích krocích. Z **spustit**, vyberte **příkazového řádku** otevřete okno příkazového řádku.  Zadejte:

        C:\> F:
        F:\> mkdir \MongoData
        F:\> mkdir \MongoLogs
7. Chcete-li spustit databázi, spusťte:

        F:\> C:
        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

    Všechny zprávy protokolu jsou směrované *F:\MongoLogs\mongolog.log* souborů jako mongod.exe server spustí a preallocates soubory deníku. To může trvat několik minut, než MongoDB předběžné přidělení soubory deníku a zahájit naslouchání pro připojení. Do příkazového řádku zůstává zaměřují se na tato úloha je spuštěna MongoDB instance.
8. Pokud chcete spustit prostředí pro správu MongoDB, otevřete další okno příkaz z **spustit** a zadejte následující příkazy:

        C:\> cd \my_mongo_dir\bin  
        C:\my_mongo_dir\bin> mongo  
        >db  
        test
        > db.foo.insert( { a : 1 } )  
        > db.foo.find()  
        { _id : ..., a : 1 }  
        > show dbs  
        ...  
        > show collections  
        ...  
        > help  

    Databáze je vytvořená úlohy insert.
9. Alternativně můžete nainstalovat mongod.exe jako služba:

        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install

    Je nainstalována služba s názvem MongoDB s popisem "Mongo DB". `--logpath` Možnost se musí použít k určení souboru protokolu, vzhledem k tomu, že běžící služba nemá okno příkazového řádku k zobrazení výstupu.  `--logappend` Možnost určuje, že restartování služby způsobí, že výstupní má být připojen ke stávajícímu souboru protokolu.  `--dbpath` Možnost určuje umístění do adresáře dat. Další související se službou možnosti příkazového řádku najdete v tématu [související se službou možnosti příkazového řádku][MongoWindowsSvcOptions].

    Chcete-li spustit službu, spusťte tento příkaz:

        C:\> net start MongoDB
10. Teď, když MongoDB je nainstalován a spuštěn, budete muset otevřít port v bráně Windows Firewall, abyste mohli vzdáleně připojte k MongoDB.  Z **spustit** nabídce vyberte možnost **nástroje pro správu** a potom **brány Windows Firewall s pokročilým zabezpečením**.
11. (a) v levém podokně vyberte **příchozí pravidla**.  V **akce** podokně na pravé straně vyberte **nové pravidlo...** .

    ![Brána Windows Firewall][Image1]

    b) v **pravidla Průvodce vytvořením nového příchozího**, vyberte **Port** a pak klikněte na **Další**.

    ![Brána Windows Firewall][Image2]

    c) vyberte **TCP** a potom **určité místní porty**.  Zadejte port "27017" (výchozí port MongoDB naslouchá na) a klikněte na tlačítko **Další**.

    ![Brána Windows Firewall][Image3]

    d) vyberte **povolit připojení** a klikněte na tlačítko **Další**.

    ![Brána Windows Firewall][Image4]

    e) klikněte na tlačítko **Další** znovu.

    ![Brána Windows Firewall][Image5]

    f) zadejte název pravidla, jako je například "MongoPort" a klikněte na tlačítko **Dokončit**.

    ![Brána Windows Firewall][Image6]

12. Pokud koncový bod nebylo nakonfigurovat pro MongoDB, když vytvoříte virtuální počítač, můžete provést nyní. Potřebujete pravidlo brány firewall a aby mohli vzdáleně připojit k MongoDB.

  Na portálu Azure klikněte na tlačítko **virtuálních počítačů (klasické)**, klikněte na název nového virtuálního počítače a pak klikněte na tlačítko **koncové body**.

    ![Koncové body][Image7]

13. Klikněte na tlačítko **Přidat**.

14. Přidat koncový bod s názvem "Mongo", protokol **TCP**a obě **veřejné** a **privátní** porty nastavena na "27017". MongoDB vzdálený přístup k otevření tohoto portu umožňuje.

    ![Koncové body][Image9]

> [!NOTE]
> Port 27017 je výchozí port je používán MongoDB. Tento výchozí port můžete změnit zadáním `--port` parametr při spuštění serveru mongod.exe. Zajistěte, aby dát stejné číslo portu v bráně firewall a endpoint "Mongo" v předchozích pokynů.
>
>

[MongoDownloads]: http://www.mongodb.org/downloads

[MongoWindowsSvcOptions]: http://www.mongodb.org/display/DOCS/Windows+Service


[Image1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
[Image2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
[Image3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
[Image4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
[Image5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
[Image6]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
[Image7]: ./media/install-and-run-mongo-on-win2k8-vm/menusendpointadd.png
<!-- Removed 03/08/2017. Not in new portal. -->
<!-- [Image8]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
-->
[Image9]: ./media/install-and-run-mongo-on-win2k8-vm/newendpointdetails.png
