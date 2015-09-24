# Avance-2

//código CAMARA

using UnityEngine;
using System.Collections;

public class CamCon : MonoBehaviour {
	public Player jugador;

	private Vector3 ultimaPosicion;
	private float mDistancia;


	// Use this for initialization
	void Start () {
		jugador = FindObjectOfType<Player> ();
		ultimaPosicion = jugador.transform.position;
	}
	
	// Update is called once per frame
	void Update () {
		mDistancia = jugador.transform.position.x - ultimaPosicion.x;
		transform.position = new Vector3 (transform.position.x + mDistancia, transform.position.y, transform.position.z);

		ultimaPosicion = jugador.transform.position;
	}
}

_________________________________________________________________________________________________________________________________

//codigo JUGADOR

using UnityEngine;
using System.Collections;

public class Player : MonoBehaviour {
	//Para la velocidad y salto
	public float velocidad;
	public float salto;

	//el player
	private Rigidbody2D cuerpoRigido;

	//piso
	public bool piso;
	public LayerMask quePiso;

	//colision
	private Collider2D colision;

	// Use this for initialization
	void Start () {

		cuerpoRigido = GetComponent<Rigidbody2D>();
		colision = GetComponent<Collider2D>();
	}
	
	// Update is called once per frame
	void Update () {

		piso = Physics2D.IsTouchingLayers (colision, quePiso);

		cuerpoRigido.velocity = new Vector2 (velocidad, cuerpoRigido.velocity.y); //se mueva de manera continua

		if(Input.GetKeyDown(KeyCode.Space) || Input.GetMouseButtonDown(0))//cuando toque la tecla espacio o el mouse
		{
			if(piso)
			{
				cuerpoRigido.velocity = new Vector2(cuerpoRigido.velocity.x, salto);
			}
		}

	}
}

_________________________________________________________________________________________________________________________________

//codigo PLAYER CONTROLLER

using UnityEngine;
using System.Collections;
using UnityEngine.UI;

public class PlayerController : MonoBehaviour {

    public Vector2 moving  = new Vector2();
    public static int score;
	void Start () {
            score = 0;

	}
	
	// Update is called once per frame
	void Update () {
        moving.x = moving.y = 0;

        if (Input.GetKey("right"))
        {
            moving.x = 1; 
        }
        else if (Input.GetKey("left"))
        {
            moving.x = -1; 
        }
        if (Input.GetKey("up"))
        {
            moving.y = 1;
        }
        else if (Input.GetKey("down"))
        {
            moving.y = -1;
        }
            
	}
    
}

_________________________________________________________________________________________________________________________________

//codigo Generar PLATAFORMAS Y MONEDAS

using UnityEngine;
using System.Collections;

public class PlatGen : MonoBehaviour {
	public GameObject laPlat;
	public Transform coordenada;
	public float disInter;

	private float platAcho;

	public float platAnchoMin;
	public float platAnchoMax;

	//public GameObject[] theArray;
	private int formSelector;
	private float [] platFormsW;

	public objectcr [] theObj2;

	//
	private float altMin;
	public Transform maxAltCoor;
	private float altMax;
	public float maxAltCam;
	private float alturaCam;

	private coinGenerator theCoin;
	public float randomCoin;


	// Use this for initialization
	void Start () {
		platFormsW = new float[theObj2.Length];

		for (int i= 0; i<theObj2.Length; i++) {
		
			platFormsW[i]=theObj2[i].pooObj.GetComponent<BoxCollider2D>().size.x;
		}
		altMin = transform.position.y;
		altMax = maxAltCoor.position.y;

		theCoin = FindObjectOfType<coinGenerator> ();
	}
	
	// Update is called once per frame
	void Update () {
		if (transform.position.x < coordenada.position.x) 
		{
			disInter=Random.Range(platAnchoMin, platAnchoMax);

			formSelector=Random.Range(0, theObj2.Length);

			alturaCam= transform.position.y+Random.Range(maxAltCam,-maxAltCam);

			if (alturaCam>altMax){

				alturaCam=altMax;
			
			}else if (alturaCam<altMin){

				alturaCam=altMin;
			}


			transform.position = new Vector3(transform.position.x + (platFormsW[formSelector]/2)+ disInter, alturaCam, transform.position.z);

			//Instantiate (/*laPlat*/ theObj2[formSelector], transform.position, transform.rotation);

			GameObject newPlat=theObj2[formSelector].getPooledObject();

			newPlat.transform.position=transform.position;
			newPlat.transform.rotation=transform.rotation;
			newPlat.SetActive(true);

			if(Random.Range(0f,100f)<randomCoin){

				theCoin.SpawnCoins(new Vector3(transform.position.x, transform.position.y +1f, transform.position.z));
			}
			transform.position = new Vector3(transform.position.x + (platFormsW[formSelector]/2),transform.position.y, transform.position.z);

		}

	}
}

_________________________________________________________________________________________________________________________________

// Código POSICIONAR MONEDAS

using UnityEngine;
using System.Collections;

public class coinGenerator : MonoBehaviour {
	public objectcr coinPool;
	public float distanceBet;
	
	public void SpawnCoins(Vector3 startPosition ){
		
		GameObject coin1 = coinPool.getPooledObject ();
		coin1.transform.position = startPosition;
		coin1.SetActive (true);
		/*
		GameObject coin2 = coinPool.getPooledObject ();
		coin2.transform.position = new Vector3 (startPosition.x - distanceBet, startPosition.y, startPosition.z);
		coin2.SetActive (true);
		
		GameObject coin3 = coinPool.getPooledObject ();
		coin3.transform.position = new Vector3 (startPosition.x + distanceBet, startPosition.y, startPosition.z);
		coin3.SetActive (true);*/
	}
}

_________________________________________________________________________________________________________________________________


//codigo DESTRUIR MONEDAS PLATAFORMAS

using UnityEngine;
using System.Collections;

public class PlatDes : MonoBehaviour {

	public GameObject platDesCoor;

	// Use this for initialization
	void Start () {
		platDesCoor = GameObject.Find ("PlatDesP");
	}
	
	// Update is called once per frame
	void Update () {

		if (transform.position.x<platDesCoor.transform.position.x)
		{
			//Destroy (gameObject);
			gameObject.SetActive(false);
		}
	}
}

________________________________________________________________________________________________________

//codigo CREAR LISTAS PARA RECICLAR PLATARFORMAS Y MONEDAS

using UnityEngine;
using System.Collections;
using System.Collections.Generic;

public class objectcr : MonoBehaviour {
	public GameObject pooObj;
	public int poolAm;

	List<GameObject> poolObjcts;
	// Use this for initialization
	void Start () {

		poolObjcts = new List<GameObject> ();
		for (int i=0; i<poolAm; i++) {
			GameObject obj = (GameObject)Instantiate (pooObj);
			obj.SetActive (false);
			poolObjcts.Add (obj);
		}
	}
	public GameObject getPooledObject(){
		for (int i=0; i<poolObjcts.Count; i++) {
			if (!poolObjcts[i].activeInHierarchy){
				return poolObjcts[i];
			}
		}
		GameObject obj = (GameObject)Instantiate (pooObj);
		obj.SetActive (false);
		poolObjcts.Add (obj);
		return obj;
	}
}

__________________________________________________________________________________________________________

//CÓDIGO CAMBIAR ESCENAS

using UnityEngine;
using System.Collections;

public class LoadLevel : MonoBehaviour {

    public void ChangeScene(string sceneDirection)
    {
        Application.LoadLevel(sceneDirection);
    }

}

__________________________________________________________________________________________________________

//CODIGO DE REINICIO

using UnityEngine;
using System.Collections;

public class GameManager : MonoBehaviour {

    public Transform platformGenerator;
    private Vector3 platformStartPoint;

    public PlayerController thePlayer;
    private Vector3 playerStartPoint;

    private PlatformDestroyer[] platformList;



	// Use this for initialization
	void Start () {

        platformStartPoint = platformGenerator.position;
        playerStartPoint = thePlayer.transform.position;

	
	}
	
	// Update is called once per frame
	void Update () {
	
	}

    public void RestartGame()
    {
        StartCoroutine("RestarGameCo");
    }  

    public IEnumerator RestartGameCo()
    {
        thePlayer.gameObject.SetActive(false);
        yield return new WaitForSeconds(0.5f);
        platformList = FindObjectsOfType<PlatformDestroyer>();
        for(int i =0; i < platformList.Lenght; i++)
        {
            platformList[i].gameObject.SetActive(false)
        }
        thePlayer.transform.position = platformStartPoint;
        platformGenerator.position = platformStartPoint;
        thePlayer.gameObject.SetActive(true);
    }
}

___________________________________________________________________________________________________________

//CODIGO SUMAR PUNTOS

using UnityEngine;
using System.Collections;

public class Collectables : MonoBehaviour
{
    public AudioSource source;
    public AudioClip sonido;
    public int scoreCoin;

    private ScoreManager theScoreManager;

    void Start()
    {
        theScoreManager = FindObjectOfType<ScoreManager>();
    }
    void OnTriggerEnter2D(Collider2D target)
    {
        if (target.gameObject.tag == "Player")
            source.PlayOneShot(sonido);
            Destroy(gameObject);
            
            theScoreManager.addScore(scoreCoin);
            
            
            
    }
}

___________________________________________________________________________________________________________

//codigo SCORE MANAGER

using UnityEngine;
using UnityEngine.UI;
using System.Collections;

public class ScoreManager : MonoBehaviour {

    public Text scoreText;
    public Text highScoreText;

    public float scoreC;
    public float highScoreC;
    public float pointsPSecond;

    public bool scoreUp;

    // Use this for initialization
	void Start () {
	
	}
	
	// Update is called once per frame
	void Update () {
        if (scoreUp) { 
            scoreC += pointsPSecond * Time.deltaTime;
        }
        if (scoreC > highScoreC)
        {
            highScoreC = scoreC;
        }
        scoreText.text = "Score: " + Mathf.Round(scoreC);
        highScoreText.text = "High Score: " + Mathf.Round(highScoreC);
	}
    public void addScore(int points)
    {
        scoreC += points;
    }
}

____________________________________________________________________________________________________________

//CODIGO RANDOM SPRITE

using UnityEngine;
using System.Collections;


public class RandomSprite : MonoBehaviour {
    public Sprite[] sprites;
    public string resourceName;

	// Use this for initialization
	void Start () {
        if (resourceName != "")
            sprites = Resources.LoadAll<Sprite>(resourceName);
            GetComponent<SpriteRenderer>().sprite = sprites[Random.Range(0, sprites.Length)];
	
	}
	
	// Update is called once per frame
	void Update () {
	
	}
}

