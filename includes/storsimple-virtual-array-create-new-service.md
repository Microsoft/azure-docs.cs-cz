#### <a name="to-create-a-new-service"></a>Vytvoření nové služby

1.  Pomocí přihlašovacích údajů účtu Microsoft se přihlaste k portálu Azure na této adrese URL: <https://portal.azure.com/>. Pokud nasazení zařízení na portál Government, přihlaste se na: <https://portal.azure.us/>

2.  Na portálu Azure klikněte na tlačítko **+ vytvořit prostředek** &gt; **úložiště** &gt; **virtuální řady StorSimple**.

    ![Vytvoření nové služby](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  V **Manager zařízení StorSimple** okno, které se otevře, proveďte následující:

    1.  Zadejte pro službu jedinečný **Název prostředku**. Název prostředku je popisný název, který slouží k identifikaci služby. Název může být tvořen 2 až 50 písmeny, číslicemi a spojovníky. Název musí začínat a končit písmenem nebo číslicí.

    2.  V rozevíracím seznamu **Předplatné** vyberte předplatné. Předplatné je propojeno s vaším fakturačním účtem. Toto pole není dostupné, pokud máte pouze jedno předplatné.

    3.  Pro **skupiny prostředků**, vyberte existující, nebo vytvořte novou skupinu. Další informace najdete v tématu [Skupiny prostředků Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

    4.  Zadejte **Umístění** služby. V tématu [oblasti Azure](https://azure.microsoft.com/regions/#services) Další informace o tom, které služby jsou dostupné v konkrétních oblastech. Obecně platí, vyberte **umístění** nejbližší zeměpisnou oblast, ve které chcete nasadit zařízení. Zvažte i následující okolnosti:

        -   Pokud máte existující úlohy v Azure, který chcete nasadit se zařízením StorSimple, doporučujeme vám, že používáte stejné datové centrum.

        -   Správce zařízení StorSimple a Azure úložiště může být na dvě samostatná místa. V takovém případě je nutné vytvořit Správce zařízení StorSimple a účet úložiště Azure odděleně. Pokud chcete vytvořit účet úložiště Azure, přejděte na webu Azure Portal do služby Azure Storage a postupujte podle kroků popsaných v tématu [Vytvoření účtu služby Azure Storage](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account). Po vytvoření účtu jej přidejte do služby Správce zařízení StorSimple pomocí kroků popsaných v tématu [Konfigurace nového účtu úložiště pro službu](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service).

        -   Pokud nasazujete virtuální zařízení na portálu Government, služba Správce zařízení StorSimple je k dispozici v nám Iowa a Virginia nám umístění.

    5.  Vyberte **vytvořit nový účet úložiště Azure** pro automatické vytvoření účtu úložiště se službou. Zadejte **název účtu úložiště**. Pokud potřebujete mít data v jiném umístění, zaškrtnutí políčka zrušte.

    6.  Pokud chcete mít na řídicím panelu rychlý odkaz na tuto službu, zaškrtněte políčko **Připnout na řídicí panel**.

    7.  Kliknutím na **Vytvořit** vytvořte Správce zařízení StorSimple.

        ![Vytvoření nové služby](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

Budete přesměrováni **služby** cílová stránka. Vytvoření služby trvá několik minut. Po úspěšném vytvoření služby budete odpovídajícím způsobem upozorněni a stav služby se změní na **Aktivní**.


