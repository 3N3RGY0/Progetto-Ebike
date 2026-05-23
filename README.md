#include <iostream>
#include <cmath>
#include <algorithm>
#include <cstdlib>
#include <ctime>
#include <iomanip>

using namespace std;

const double VELOCITA_MEDIA = 20.0;
const double TARIFFA_MINUTO = 0.20;
const int NUM_BICI = 5;

struct EBike {
    int id;
    double x;
    double y;
    double autonomia;
    bool disponibile;
    double distanza;
};

bool confrontaDistanza(const EBike &a, const EBike &b) {
    return a.distanza < b.distanza;
}

double calcolaDistanza(double x1, double y1, double x2, double y2) {
    return sqrt(pow(x1 - x2, 2) + pow(y1 - y2, 2));
}

int main() {
    srand(time(0));

    EBike flotta[NUM_BICI] = {
        {101, 2.5, 3.1, 45.0, true, 0.0},
        {102, -1.0, 4.5, 12.0, true, 0.0},
        {103, 5.0, -2.0, 5.0,  true, 0.0}, 
        {104, 0.5, 0.5, 30.0, false, 0.0}, 
        {105, -3.2, -1.5, 60.0, true, 0.0}
    };

    double xUtente, yUtente;
    cout << "E-bike a nolegio" << endl;
    cout << "Inserisci la tua coordinata X attuale: ";
    cin >> xUtente;
    cout << "Inserisci la tua coordinata Y attuale: ";
    cin >> yUtente;

    int scelta;
    do {
      
        cout << "Menu Prinicipale" << endl;
       
        cout << "1. Visualizza e-bike disponibili" << endl;
        cout << "2. Noleggia una e-bike" << endl;
        cout << "0. Esci dal programma" << endl;
       
        cout << "Scelta: ";
        cin >> scelta;

        switch (scelta) {
            case 1: {
                for (int i = 0; i < NUM_BICI; ++i) {
                    flotta[i].distanza = calcolaDistanza(flotta[i].x, flotta[i].y, xUtente, yUtente);
                }
                sort(flotta, flotta + NUM_BICI, confrontaDistanza);

                cout << "Mezzi disponibili nelle vicininze" << endl;
                cout << left << setw(8) << "ID" << setw(15) << "Coordinate" << setw(15) << "Autonomia" << setw(12) << "Distanza" << endl;


                bool almenoUnaDisponibile = false;
                for (int i = 0; i < NUM_BICI; ++i) {
                    if (flotta[i].disponibile) {
                        almenoUnaDisponibile = true;
                        cout << left << setw(8) << flotta[i].id 
                             << "(" << flotta[i].x << ", " << flotta[i].y << ")\t" 
                             << left << setw(15) << (to_string((int)flotta[i].autonomia) + " km")
                             << fixed << setprecision(2) << flotta[i].distanza << " unita'" << endl;
                    }
                }
                if (!almenoUnaDisponibile) {
                    cout << "Nessuna e-bike disponibile al momento." << endl;
                }
                break;
            }
            case 2: {
                int idScelto;
                cout << "Inserisci l'ID della e-bike da noleggiare: ";
                cin >> idScelto;

                int indiceBici = -1;
                for (int i = 0; i < NUM_BICI; ++i) {
                    if (flotta[i].id == idScelto && flotta[i].disponibile) {
                        indiceBici = i;
                        break;
                    }
                }
                if (indiceBici == -1) {
                    cout << "Errore: ID non valido o e-bike non disponibile per il noleggio." << endl;
                    break;
                }

                double kmDaPercorrere;
                cout << "Quanti km intendi percorrere con la e-bike " << idScelto << "? ";
                cin >> kmDaPercorrere;

                if (kmDaPercorrere > flotta[indiceBici].autonomia) {
                    cout << "Noleggio Negato: I km richiesti superano quelli della bici (" << flotta[indiceBici].autonomia << " km)." << endl;
                    break;
                }

                int ritardoCasuale = rand() % 15 + 1;
                double tempoStimatoMinuti = (kmDaPercorrere / VELOCITA_MEDIA) * 60.0 + ritardoCasuale;


                flotta[indiceBici].autonomia -= kmDaPercorrere;
                double costoFinale = tempoStimatoMinuti * TARIFFA_MINUTO;

                cout << "Riepilogo viaggio" << endl;
                cout << "Distanza percorsa:      " << kmDaPercorrere << " km" << endl;
                cout << "Tempo totale stimato:   " << round(tempoStimatoMinuti) << " minuti" << endl;
                cout << " Incluso ritardo di:   " << ritardoCasuale << " min" << endl;
                cout << "Tariffa al minuto:      " << TARIFFA_MINUTO << " EUR/min" << endl;
                cout << "Costo finale dovuto:    " << fixed << setprecision(2) << costoFinale << " EUR" << endl;
                cout << "Nuova autonomia mezzo:  " << flotta[indiceBici].autonomia << " km" << endl;
                

                flotta[indiceBici].disponibile = true; 
                cout << "La e-bike #" << flotta[indiceBici].id << " e' stata rilasciata con successo." << endl;
                break;
            }
            case 0:
                cout << "Grazie per aver utilizzato E-bike a noleggio." << endl;
                break;
            default:
                cout << "Opzione non valida" << endl;
                break;
        }
    } while (scelta != 0);

    return 0;
}
