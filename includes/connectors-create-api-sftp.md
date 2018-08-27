### <a name="prerequisites"></a>Požadavky
* [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol) účtu  

Před použitím svého účtu SFTP v aplikaci logiky, musíte se autorizovat aplikaci logiky pro připojení k účtu SFTP. Naštěstí vám pomůžou snadno z v rámci aplikace logiky na webu Azure Portal.  

Tady jsou kroky k autorizaci aplikace logiky pro připojení k účtu SFTP:  

1. Chcete-li vytvořit připojení k protokolu SFTP, v návrháři aplikace logiky, vyberte **rozhraní API spravovaná Microsoftem zobrazit** v rozevíracím seznamu zadejte *SFTP* do vyhledávacího pole. Vyberte **SFTP – když se přidá nebo upraví soubor** aktivační události:  
   ![Obrázek online připojení SFTP 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. Pokud jste ještě nevytvořili žádná připojení k protokolu SFTP před, zobrazí výzva zadat přihlašovací údaje protokolu SFTP. Tyto přihlašovací údaje se použijí k autorizaci aplikace logiky pro připojení k a přístup k účtu SFTP data:  
   ![Obrázek online připojení SFTP 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. Všimněte si, připojení bylo vytvořeno a teď jste zdarma postupujte podle dalších kroků ve vaší aplikaci logiky:   
   ![Obrázek online připojení SFTP 3](./media/connectors-create-api-sftp/sftp-3.png) 

