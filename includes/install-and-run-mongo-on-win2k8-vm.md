Postupujte podle těchto kroků nainstalujte a spusťte MongoDB na virtuálním počítači s Windows serverem.

> [!IMPORTANT]
> MongoDB funkce zabezpečení, jako je například ověřování a vazbu adresy IP, nejsou povolené ve výchozím nastavení. Funkce zabezpečení musí být aktivována před nasazením do produkčního prostředí MongoDB.  Další informace najdete v tématu [zabezpečení a ověřování](http://www.mongodb.org/display/DOCS/Security+and+Authentication).
>
>

1. Po připojení k virtuálnímu počítači pomocí vzdálené plochy, spusťte aplikaci Internet Explorer z **Start** nabídky na virtuálním počítači.
2. Vyberte **nástroje** tlačítko v pravém horním rohu.  V **Možnosti Internetu**, vyberte **zabezpečení** kartu a potom vyberte **Důvěryhodné servery** ikony a nakonec klikněte **lokality** tlačítko. Přidat *https://\*. mongodb.org* do seznamu důvěryhodných webů.
3. Přejděte na [stáhne - MongoDB](https://www.mongodb.com/download-center#community).
4. Najít **aktuální stabilní verzi** z **Community Server**, vyberete možnost nejnovější **64-bit** verze ve sloupci Windows. Stáhněte a spusťte instalační program MSI.
5. MongoDB je obvykle nainstalován v C:\Program Files\MongoDB. Hledat proměnné prostředí v klientských počítačích a přidat cestu binární soubory MongoDB do proměnné PATH. Například může vyhledat binární soubory na C:\Program Files\MongoDB\Server\3.4\bin na svém počítači.
6. Vytvoření adresářů protokolu a data MongoDB v datového disku (například disk **F:**) jste vytvořili v předchozích krocích. Z **Start**vyberte **příkazového řádku** otevřete okno příkazového řádku.  Zadejte:

        C:\> F:
        F:\> mkdir \MongoData
        F:\> mkdir \MongoLogs
7. Ke spuštění databáze, spusťte:

        F:\> C:
        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

    Všechny zprávy protokolu jsou směrované na *F:\MongoLogs\mongolog.log* souborům mongod.exe server spustí a preallocates soubory deníku. Může trvat několik minut, než MongoDB předběžné přidělení soubory deníku a zahájit naslouchání pro připojení. Příkazový řádek zůstává zaměřují se na tato úloha je spuštěna MongoDB instance.
8. Spustit MongoDB shell pro správu, otevřete další okno příkazu z **Start** a zadejte následující příkazy:

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

    Databáze se vytvoří pomocí insert.
9. Alternativně můžete nainstalovat mongod.exe jako služba:

        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install

    Je nainstalována služba s názvem MongoDB s popisem "Mongo DB". `--logpath` Parametr musí být použit k určení souboru protokolu, protože nemá spuštěnou službu příkazové okno k zobrazení výstupu.  `--logappend` Možnost určuje, že restartování služby způsobí, že výstupní má připojit k existujícímu souboru protokolu.  `--dbpath` Určuje umístění adresáře data. Informace týkající se služeb možnosti příkazového řádku najdete v tématu [související se službou možnosti příkazového řádku][MongoWindowsSvcOptions].

    Chcete-li spustit službu, spusťte tento příkaz:

        C:\> net start MongoDB
10. Teď, když MongoDB je nainstalovaná a spuštěná, musíte otevřít port v bráně Windows Firewall, abyste se mohli vzdáleně připojit k MongoDB.  Z **Start** nabídce vyberte možnost **nástroje pro správu** a potom **brány Windows Firewall s pokročilým zabezpečením**.
11. ) v levém podokně vyberte **příchozí pravidla**.  V **akce** podokno na pravé, vyberte **nové pravidlo...** .

    ![Brána Windows Firewall][Image1]

    (b) v **pravidla Průvodce vytvořením nového příchozího**vyberte **Port** a potom klikněte na tlačítko **Další**.

    ![Brána Windows Firewall][Image2]

    c) vyberte **TCP** a potom **určité místní porty**.  Zadejte port "27017" (výchozí port MongoDB naslouchá na) a klikněte na tlačítko **Další**.

    ![Brána Windows Firewall][Image3]

    d) vyberte **povolit připojení** a klikněte na tlačítko **Další**.

    ![Brána Windows Firewall][Image4]

    (e) klikněte na **Další** znovu.

    ![Brána Windows Firewall][Image5]

    f) zadejte název pravidla, jako je například "MongoPort" a klikněte na tlačítko **Dokončit**.

    ![Brána Windows Firewall][Image6]

12. Koncový bod nebyl konfiguraci pro MongoDB, při vytváření virtuálního počítače, proveďte to nyní. Potřebujete pravidlo brány firewall a endpoint umožnit vzdálené připojení k MongoDB.

  Na webu Azure Portal, klikněte na tlačítko **virtuální počítače (classic)**, klikněte na název nového virtuálního počítače a potom klikněte na tlačítko **koncové body**.

    ![Koncové body][Image7]

13. Klikněte na tlačítko **Add** (Přidat).

14. Přidat koncový bod s názvem "Mongo", protokol **TCP**a obě **veřejné** a **privátní** porty nastavena na "27017". Otevřít tento port umožňuje MongoDB jde přistupovat vzdáleně.

    ![Koncové body][Image9]

> [!NOTE]
> Port 27017 je výchozí port je používán MongoDB. Tento výchozí port můžete změnit zadáním `--port` parametr při spuštění serveru mongod.exe. Ujistěte se, že poskytují stejné číslo portu v bráně firewall a endpoint "Mongo" v předchozích pokynech.
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
