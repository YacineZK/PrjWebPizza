# PrjWebPizza
# Project to sell pizzas online .Net C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using System.Data;         // 
using System.Data.OleDb;  // pour les base de donnee access

namespace prjWebCsAppPizzaDB
{
    public partial class SiteWebPizzariaNapolitana : System.Web.UI.Page
       
    {
        static OleDbConnection mycon;
        protected void Page_Load(object sender, EventArgs e)
        {
            //panPanier.Visible = false;
            //btnAjouter.Visible = false;
            //litComInfo.Visible = false;



            //if (Page.IsPostBack == false) // pour eviter la repietition dans les list box
            //{
            //    1 Connexion a la dbPizzNapol
            //    mycon = new OleDbConnection();
            //    mycon.ConnectionString = @"Provider=Microsoft.Jet.OLEDB.4.0;Data Source=C:\Users\Admin\source\repos\prjWebCsAppPizzaDB\prjWebCsAppPizzaDB\App_Data\dbPizzaNapol.mdb;Persist Security Info=True";
            //    mycon.Open();

            //    2 Requete Sql pour la selection
            //   OleDbCommand mycmd = new OleDbCommand();
            //    mycmd.CommandText = "SELECT Nom,PrixUnitaire FROM  Pizzas";
            //    mycmd.Connection = mycon;

            //    OleDbDataReader myrder = mycmd.ExecuteReader();

            //    3 remplissage de la listeBox Choix pizza avec du contenu depuis notre Db creer
            //    while (myrder.Read() == true)
            //    {
            //        ListItem elm = new ListItem();
            //        elm.Text = myrder["Nom"].ToString();
            //        elm.Value = myrder["PrixUnitaire"].ToString();
            //        lstChoixPizza.Items.Add(elm);
            //    }
            //    RemplirListTailles(); // remplissage de la list des tailles 
            //    RemplirListGarnitures();//remplissage de la checkbox  garnitures
            //    RemplirListCroutes();//remplissage de la liste des croute
            //    CalculerPrix();



            //    mycon.Close();
            //}

        }

        protected void RemplirListCroutes()
        {
            string sql = "SELECT Croute, PrixUnitaire FROM Croutes";
            OleDbCommand mycmd = new OleDbCommand(sql, mycon);
            OleDbDataReader myrder = mycmd.ExecuteReader();
            while (myrder.Read() == true)
            {
                ListItem elm = new ListItem();
                elm.Text = myrder["croute"].ToString();
                elm.Value = myrder["PrixUnitaire"].ToString();
                lstChoixCroutes.Items.Add(elm);

            }
            myrder.Close();
        }
        protected void RemplirListGarnitures()
        {
            string sql = "SELECT garniture, PrixUnitaire FROM Garnitures";
            OleDbCommand mycmd = new OleDbCommand(sql, mycon);
            OleDbDataReader myrder = mycmd.ExecuteReader();
            while (myrder.Read() == true)
            {
                ListItem elm = new ListItem();
                elm.Text = myrder["garniture"].ToString();
                elm.Value = myrder["PrixUnitaire"].ToString();
                chkGarniture.Items.Add(elm);

            }
            myrder.Close();
        }
        protected void RemplirListTailles() 
        {
            string sql = "SELECT Nom, Facteur FROM Tailles";
            OleDbCommand mycmd = new OleDbCommand(sql, mycon);
            OleDbDataReader myrder = mycmd.ExecuteReader();
            while(myrder.Read()==true)
            {
                ListItem elm = new ListItem();
                elm.Text = myrder["Nom"].ToString();
                elm.Value = myrder["Facteur"].ToString();
                lstChoixTailles.Items.Add(elm);
            
            }
            myrder.Close();
        }

        protected void btnChercher_Click(object sender, EventArgs e)
        {
            mycon.Open();
            string num = txtNumTel.Text.Trim().Replace(" ", "");
            string sql = "SELECT Nom, Telephone, Adresse FROM Clients WHERE Telephone = '" + num + "'";

            OleDbCommand mycmd = new OleDbCommand(sql, mycon);
            OleDbDataReader myRder = mycmd.ExecuteReader();

            if (myRder.Read() == true)
            {
                txtNom.Text = myRder["Nom"].ToString();
                txtAdresse.Text = myRder["Adresse"].ToString();
                mycon.Close();
                btnAjouter.Visible = false;
                return; 
            }
            txtNom.Text = "Ajouter Nom complet ";
            txtNumTel.Text = "Ajouter Numero ";
            txtAdresse.Text = "Ajouter l'adresse du client ";
            txtNom.Focus();
            mycon.Close();

            btnAjouter.Visible = true;
        }

        protected void chkLiv_CheckedChanged(object sender, EventArgs e)
        {
            btnAjouter.Visible = false;
            mycon.Open();
            string tel = txtNumTel.Text.Trim().Replace(" ", "");
            string ad = txtAdresse.Text.Trim();
            string nom = txtNom.Text.Trim();
            string sql = "INSERT INTO Clients(Nom, Telephone, Adresse) VALUES ('" + nom + "','" + tel + "','" + ad + "')";
            OleDbCommand mycmd = new OleDbCommand(sql, mycon);
            mycmd.ExecuteNonQuery();
            mycon.Close();
        }

        private void CalculerPrix()
        {
            panPanier.Visible = true;
            string info;
            decimal prix2base = 0;
            decimal liv = 0;
            decimal total;
            decimal garni = 0;
            decimal croute = 0;

            croute = Convert.ToDecimal(lstChoixCroutes.SelectedItem.Value);

            prix2base = Convert.ToDecimal(lstChoixPizza.SelectedItem.Value);

            
            liv = (chkLiv.Checked) ? 3 : 0;
            prix2base = prix2base * Convert.ToDecimal(lstChoixTailles.SelectedItem.Value);


            //calcul total des garnitures
            foreach (ListItem elmt in chkGarniture.Items)
            {
                if (elmt.Selected)
                {
                    garni += Convert.ToDecimal(elmt.Value);

                }
            }

            total = prix2base + liv + croute;
            decimal taxe = total * 14 / 100;  // variable pour calculer la taxe



            info = "Prix de base : " + prix2base + " $  <br />";
            info += "Livraison : " + liv + " $  <br />";
            info += "Garnitures : " + garni + " $  <br />"; // ce code permet l'affichage des montant des garnitures selectioner.
            info += "        ----------------" + "<br />";
            info += "Sous-Total : " + total + " $  <br />";
            info += "Taxe (14%) : " + taxe + " $  <br />";

            info += "        ----------------" + "<br />";
            info += "Total : " + (total + taxe) + " $  <br />";

            litComInfo.Text = "<b>" + info + "</b>";

            panPanier.Visible = true;

        }

        protected void btnValider_Click(object sender, EventArgs e)
        {
            panPanier.Visible = true;
            litComInfo.Visible = true;


            string info = "La commande de " + txtNom.Text + 
                " d'une " + lstChoixTailles.SelectedItem.Text +" pizza " +
                lstChoixPizza.SelectedItem.Text + "<br />";
            

            if(chkLiv.Checked)
            {
                info += " A etre livree au " + txtAdresse.Text + "<br />";
            }
            else
            {
                info += " A etre ramasser <br />";
            }
            info += "Telephone :" + txtNumTel.Text + "<br />";

            if(chkGarniture.SelectedIndex!= -1) // au moin une garniture selectionner
            {
                info += " Avec les garniture : <br /> <ul type='square'>";
                foreach (ListItem elmt in chkGarniture.Items)
                {
                    if(elmt.Selected==true)
                    {
                        info += "<li>" + elmt.Text + "</li>";
                    }
                    
                }
                info += "</ul>";
            }
            info += " Sur une croute " + lstChoixCroutes.SelectedItem.Text +
                " a ete placee le " + DateTime.Today.ToShortDateString() + " a " + DateTime.Now.ToShortTimeString() +
                " min <br />";

            litComInfo.Text = info;

        }

        protected void lstChoixPizza_SelectedIndexChanged(object sender, EventArgs e)
        {
           
        }
    }
    
}
