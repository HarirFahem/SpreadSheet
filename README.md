## Application Using Main Window
create a MainWindow based application using the designer
- [Introduction](#INTRO)
- [SpreadSheet](#SpreadSheet)
- [TextEditor](#TextEditor)

<div id = "back"></div>


    
## **Introduction** 

<a name="INTRO"></a>

"" ** “Programs must be written for people to read, and only incidentally for machines to execute.” ** ""

**QMainWindow** is a main window provides a framework for building an application's user interface. Qt has QMainWindow and its related classes for main window management. QMainWindow has its own layout to which you can add QToolBars, QDockWidgets, a QMenuBar, and a QStatusBar. The layout has a center area that can be occupied by any kind of widget. You can see an image of the layout below.

![Image](/qmainwindoww.png)

**Qt Designer** is the Qt tool for designing and building graphical user interfaces (GUIs) with Qt Widgets. You can compose and customize your windows or dialogs in a what-you-see-is-what-you-get (WYSIWYG) manner, and test them using different styles and resolutions.

Widgets and forms created with Qt Designer integrate seamlessly with programmed code, using Qt's signals and slots mechanism, so that you can easily assign behavior to graphical elements. All properties set in Qt Designer can be changed dynamically within the code. Furthermore, features like widget promotion and custom plugins allow you to use your own components with Qt Designer.

![Image](/qtdesigner.png)

 >**In This Zip you will have the project** [Homework3.zip]() 


### SpreadSheet 
<a name="SpreadSheet"></a>
We wrote the code for the graphical and set of actions for our main SpreadSheet application, now we will write a set of basic functionality.
we will start with the connections made for spreadsheet:

1.the **updateStatusBar** takes two ints in order to synchronize with the selected item from spreadsheet, it changes the cellLocation text with the current cell coordinate.
```javascript
void updateStatusBar(int, int); //Respond for the call changed
```
Here is the implementation of this function
```javascript
void SpreadSheet::updateStatusBar(int row, int col)
{
    QString cell{"(%0, %1)"};
   cellLocation->setText(cell.arg(row+1).arg(col+1));

}
```
2.we add the function for the **gocell** action,we created a Dialog for the user to select a cell, for that, first we created a Form Class, the using the designer we obtain the form of cell location, in addition we added the regular expression validator for the lineEdit , and finally we added a public getter for the line edit text to get the cell address.
Here is the Form of Cell location:

![Image](/cellLocation.png)
UI components of the Go Dialog

Here is the code of the regular expression validator and the add of the pubic Getter
```javascript

    ui(new Ui::GoDialog)
{
    ui->setupUi(this);
    //Définir l'expression nregulière
    QRegExp reg{"[A-Z][1-9][0-9]"};
    //validateur pour le lineedit
    ui->lineEdit_2->setValidator(new QRegExpValidator(reg));

}
QString GoDialog::getText()const
{
    return ui->lineEdit_2->text();
}

```
We created a connexion between the GoCell action:
first we create the private slot called goCellSlot to respond to the trigger:

```javascript
private slots:
    void GotoCellSlots();
```
Then we connected the action to its proper slot in the makeConnexions function:

```javascript
   //connection entre le go et le slot
   connect(goCell,&QAction::triggered,this,&SpreadSheet::GotoCellSlots);
```
and there in the implementation for the GotoCellSlots() function:

```javascript
void SpreadSheet::GotoCellSlots()
{
    //declarer le dialog
    GoDialog D;
    //l'executer
    auto repl = D.exec();
    if(repl==GoDialog::Accepted)
    {  //extraire le row et le column
        auto text = D.getText();
        int row = text[0].toLatin1()-'A';
        //supprimer le premier caractere
        text= text.remove(0,1);
        int col =text.toInt();
        spreadsheet->setCurrentCell(row,col);
    }

}
```
![Image](/gotocell.png)

GotoCell dialog and response

3.We added Find Dialog, it prompts the user for an input and seek a cell that contains the entered text. For that , We created this following Ui:

![Image](/search.png)

![Image](/search2.png)

Find Dialog ui form 

Then, we added a getter to obtain the serached text:

```javascript
QString FindDialog::getText() const
{
    return ui->lineEdit->text();
}
```
we have declared a private slot called search:
```javascript
private slots:
    void Search();
```
Here is the implementation of the connexion between the dialog and the find function:

```javascript
void SpreadSheet::Search()
{
  FindDialog F;
  auto repl = F.exec();
  if(repl == FindDialog::Accepted){
      auto text=F.getText();
      for(auto i =0;i<spreadsheet->rowCount();i++){
          for(auto j =0;j<spreadsheet->columnCount();j++){
            if(spreadsheet-> item(i,j)!=nullptr && spreadsheet-> item(i,j)->text().contains(text) )
                spreadsheet->setCurrentCell( i, j);
      }
  }
  }
}
```
![Image](/finddialog.png)

Find Dialog illustration

4.saving the file, either the function save or save as, those functions allow saving the data to a storage location, we choose a simple format that store the coordinates and the content of the non empty cells
* for the save function , we wrote a private function  
```markdown
private:
void saveContent(QString fileName); 
```
Here is the implementation of this function:
```javascript
void SpreadSheet::saveContent(QString fileName)
{
    //ouvrir le fichier en mode de lecture
    QFile file(fileName);
    if(file.open(QIODevice::WriteOnly)){
     QTextStream out(&file);
     //boucle sur les cellules pour sauvergarder leur contenu
     for(int i=0;i<spreadsheet->rowCount();i++)
         for(int j=0;j<spreadsheet->columnCount();j++)
         {
             auto cell = spreadsheet->item(i,j);
             if(cell){
                 out <<i << "," <<j <<","<< cell->text() << endl;
             }
         }
    }
    //fermer la connexion avec le fichier
    file.close();

}
```
We declared a private slot called saveslot:
```markdown
private slots:
     void saveslot();
```
that respond to the action trigger in the header, it is use to save the content of the file , here is its implementation:
```javascript
void SpreadSheet::saveslot()
{
  //vérifier si on a pas un nom de fichier
    if(!currentFile){
        QFileDialog D;
        auto filename=D.getSaveFileName();
        //changer le nom du fichier
        currentFile=new QString(filename);
        //changer le title de l'application
        setWindowTitle(*currentFile);
    }
    //fonction privée pour sauvergarder le contenu
    saveContent(*currentFile);
}
```
for the connexion in makeconnexions() function:
```markdown
   connect(save, &QAction::triggered,this,&SpreadSheet::saveslot);
```
![Image](/save.png)

save illustration

* For saveAs, which export the date in a specific format like csv file of pds..
We declared a slot called SaveAsSlot():
```markdown
private slots:
    void SaveAsSlot();
```
and for the implemenation of this function :
```javascript
void SpreadSheet::SaveAsSlot(){
    if(!currentFile)
    {
        QFileDialog D;
        auto filename=D.getSaveFileName(this,("save file"),"",("csv File(*.csv);;Text files (*.txt);;XML files (*.xml);;All files (*.*)"));
        //changer le nom de fichier
        currentFile = new QString(filename);
        //changer le title de l'application
        setWindowTitle(*currentFile);

    }
    saveContent(*currentFile);
}
```

![Image](/saveas.png)

saveAs illustration

5. for the load file, this function is used to import the date into spreadsheet
We declared a function called opencontent in the header file:
```javascript
private:
 void OpenContent(QString fileName );
```
Here is the implementation of this function
```javascript
void SpreadSheet::OpenContent(QString fileName){
    QFile file(fileName);
    if(file.open(QIODevice::ReadOnly)){
            QTextStream in(&file);
            while(!in.atEnd()){
                QString line;
                line=in.readLine();
                //separer par virgule
                auto tokens = line.split(QChar(','));
                //num row
                int row=tokens[0].toInt();
                int col=tokens[1].toInt();
                auto cell = new QTableWidgetItem(tokens[2]);
                spreadsheet->setItem(row,col,cell);
            }
}
}
```
For the load file action, we have an operational open function, first we create a dlot to respond to the action trigger on the header
```markdown
private slots:
   void Open();
```
Here is the implementation of this function:
```javascript
void SpreadSheet::Open(){

      QFileDialog D;
     auto filename=D.getOpenFileName(this,("open file"));

      //change the name of the file
      currentFile = new QString(filename);
      setWindowTitle(*currentFile);
      if (currentFile->endsWith(".csv"))
                loadcsv(filename);
                else OpenContent(filename);
}
```
![Image](/open.png)

open illustration

6.We added a function that allow the code for reading a csv file in our spreadsheet
We delcared a function in the header file:
```markdown
private:
   void loadcsv(QString fileName);
```
here is the implementation of this function:
```javascript
void SpreadSheet::loadcsv(QString filename){
    QFile file(filename);
    if (file.open(QIODevice::ReadOnly )) {
        QTextStream in(&file);

 int i=0;
    while (!in.atEnd()) {
        QString line = in.readLine();
        // now, line will be a string of the whole line, if you're trying to read a CSV or something, you can split the string
        auto list = line.split(QChar(';'));
        // process the line here

        for(int j=0;j<list.length();j++){
            auto contenu=new  QTableWidgetItem (list[j]);
             spreadsheet->setItem(i,j,contenu);
        }
        i++;
    }
}
}
```
![Image](/opencsv.png)

Open Csv File illustration

7.We added a function that shows the most five recent files
We declared a slot called recentfile
```markdown
private slots:
   void recentfile();
```
And here is the implementation of this function
```javascript
void SpreadSheet::recentfile()
{

    //recentfiles
    QStringList *recentFile;
 
}
```
a. We added a slot called close to quit the window
First, we declare the slot in the header file
```markdown
private slots:
   void close();
```
Then we implement the function in the cpp file 

```javascript
void SpreadSheet::close()
{

    auto reply = QMessageBox::question(this, "Exit","Do you really want to quit?");
    if(reply == QMessageBox::Yes)
        qApp->exit();
}
```
b.We added a slot called new file to open a new spreadsheet
we delcare the slot in the header file:
```markdown
private slots:
    void newfile();
```
Then, we implement the function in the cpp file:
```javascript
void SpreadSheet::newfile(){


    SpreadSheet *mainWin = new SpreadSheet;
                 mainWin->show();
}
```
![Image](/newfile.png)

new file illustration

c.for the selection Item, we have select all, select row and select column
in the function makeConnexions we have the following code:
```javascript
   connect(all, &QAction::triggered,spreadsheet, &QTableWidget::selectAll);
   connect(row, &QAction::triggered,spreadsheet, &QTableWidget::selectRow);
   connect(Column, &QAction::triggered, spreadsheet, &QTableWidget::selectColumn);
```
![Image](/selectall.png)

SelectAll 

![Image](/selectrow.png)

Select Row 

![Image](/selectcol.png)

Select Column 

d. for the delete action: we have delete all:
in the function makeConnexions() we wrote:
```javascript
   connect(deleteall, &QAction::triggered,spreadsheet, &QTableWidget::clearContents);
```
![Image](/deleteall.png)
and we have delete the content of a cell 
In the function makeConnections() we wrote:
```javascript
   connect(deleteAction, &QAction::triggered,this, &SpreadSheet::deletecell);

```
and here is the implemenation of the function deletecell() which is declared in the header file:
```javascript
void SpreadSheet::deletecell(){

    foreach (QTableWidgetItem *i, spreadsheet->selectedItems())
                i->setText("");
}
```
![Image](/deletecell.png)
e. for the item about, we have about and aboutQt :
For about , we declared a slot called aboutSlot(), then we implement it:
```javascript
void SpreadSheet::aboutSlot(){
    QMessageBox::about(this,"about", "My spreadsheet!!");
}
```
![Image](/about.png)
For aboutQT , we declared a slot named aboutQtSlot(), then we implement it:
```javascript
void SpreadSheet::aboutQtSlot(){
    QMessageBox::aboutQt(this, "Your Qt");
}
```
![Image](/aboutQt.png)
```javascript
```
```markdown
```
```javascript
```
```markdown
```
```javascript
```
We added the **makeConnexion()** to connect all the actions.
here is the content of this Function:
```javascript
void SpreadSheet::makeConnexions()
{

   // --------- Connexion for the  select all action ----/
   connect(all, &QAction::triggered,spreadsheet, &QTableWidget::selectAll);
   connect(row, &QAction::triggered,spreadsheet, &QTableWidget::selectRow);
   connect(Column, &QAction::triggered, spreadsheet, &QTableWidget::selectColumn);
   //connection for the clear all 
   connect(deleteall, &QAction::triggered,spreadsheet, &QTableWidget::clearContents);
   //connection for the delete cell content
   connect(deleteAction, &QAction::triggered,this, &SpreadSheet::deletecell);
   // Connection for the  show grid
   connect(showGrid, &QAction::triggered,spreadsheet, &QTableWidget::setShowGrid);
   //Connection for the exit button
   connect(exit, &QAction::triggered, this, &SpreadSheet::close);
   //connectting the chane of any element in the spreadsheet with the update status bar
   connect(spreadsheet, &QTableWidget::cellClicked, this,  &SpreadSheet::updateStatusBar);
   //connection entre le go et le slot
   connect(goCell,&QAction::triggered,this,&SpreadSheet::GotoCellSlots);
   connect(find,&QAction::triggered,this,&SpreadSheet::Search);
   //connection de save file
   connect(save, &QAction::triggered,this,&SpreadSheet::saveslot);
   //connection du open file
   connect(open,&QAction::triggered,this,&SpreadSheet::Open);
   //connection du new file
   connect(newFile,&QAction::triggered,this,&SpreadSheet::newfile);
   //connect du slot
   connect(about,&QAction::triggered,this,&SpreadSheet::aboutSlot);
   //connect du Qtslot
   connect(aboutQt,&QAction::triggered,this,&SpreadSheet::aboutQtSlot);
   //connect du recent files
   connect(recfiles,&QAction::triggered,this,&SpreadSheet::recentfile);
   //connect saveas
   connect(saveAs,&QAction::triggered,this,&SpreadSheet::SaveAsSlot);
}
```
Finally, we obtain:

![Image](/spread1.png)
![Image](/spread2.png)
SpreadSheet Application with actions and menus 
### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/HarirFahem/HomeWork3/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
