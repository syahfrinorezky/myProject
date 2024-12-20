let board;
let boardLebar = 360;
let boardTinggi = 640;
let context;

let avatarLebar = 70;
let avatarTinggi = 50;

let avatarX = boardLebar / 8;
let avatarY = boardTinggi / 2;

let avatarImg; // Hanya satu avatar
let avatar = {
  x: avatarX,
  y: avatarY,
  width: avatarLebar,
  height: avatarTinggi,
};

let pipaArray = [];
let pipaLebar = 64;
let pipaTinggi = 512;
let pipaX = boardLebar;
let pipaY = 0;

let topPipaImg;
let botPipaImg;

let velocityX = -2;
let velocityY = 0;
let gravitasi = 0.4;

let gameOver = false;
let score = 0;

window.onload = function () {
  board = document.getElementById("board");
  board.height = boardTinggi;
  board.width = boardLebar;
  context = board.getContext("2d");

  avatarImg = new Image(); // Inisialisasi avatar tunggal
  avatarImg.src = "../asset/avatar/jeri.png"; // Ganti sesuai avatar yang diinginkan

  avatarImg.onload = function () {
    context.drawImage(
      avatarImg,
      avatar.x,
      avatar.y,
      avatar.width,
      avatar.height
    );
  };

  topPipaImg = new Image();
  topPipaImg.src = "../asset/bahan/toppipe.png";

  botPipaImg = new Image();
  botPipaImg.src = "../asset/bahan/bottompipe.png";

  requestAnimationFrame(update);
  setInterval(tempatPipa, 1800);
  document.addEventListener("keydown", gerakBurung);
};

function update() {
  requestAnimationFrame(update);
  if (gameOver) {
    return;
  }

  context.clearRect(0, 0, boardLebar, boardTinggi);

  velocityY += gravitasi;
  avatar.y = Math.max(avatar.y + velocityY, 0);
  context.drawImage(avatarImg, avatar.x, avatar.y, avatar.width, avatar.height);

  if (avatar.y > boardTinggi) {
    gameOver = true;
  }

  for (let i = 0; i < pipaArray.length; i++) {
    let pipa = pipaArray[i];
    pipa.x += velocityX;
    context.drawImage(pipa.img, pipa.x, pipa.y, pipa.width, pipa.height);

    if (!pipa.passed && avatar.x > pipa.x + pipa.width) {
      score += 0.5;
      pipa.passed = true;
    }

    if (detectCollision(avatar, pipa)) {
      gameOver = true;
    }
  }

  while (pipaArray.length > 0 && pipaArray[0].x < -pipaLebar) {
    pipaArray.shift();
  }

  context.fillStyle = "white";
  context.font = "30px sans-serif";
  let scoreText = Math.floor(score).toString();
  let scoreTextWidth = context.measureText(scoreText).width;
  context.fillText(scoreText, (boardLebar - scoreTextWidth) / 2, 50);

  if (gameOver) {
    let gameOverText = "MAKANLAH SIKIT!";
    let gameOverTextWidth = context.measureText(gameOverText).width;

    context.fillStyle = "rgba(0, 0, 0, 0.7)";
    context.fillRect(
      (boardLebar - gameOverTextWidth - 20) / 2,
      (boardTinggi - 60) / 2,
      gameOverTextWidth + 20,
      60
    );

    context.fillStyle = "white";
    context.fillText(
      gameOverText,
      (boardLebar - gameOverTextWidth) / 2,
      boardTinggi / 2 + 15
    );
  }
}

function tempatPipa() {
  if (gameOver) {
    return;
  }

  let randomPipaY = -pipaTinggi / 4 - Math.random() * (pipaTinggi / 2);
  let openSpace = board.height / 4;

  let pipaAtas = {
    img: topPipaImg,
    x: pipaX,
    y: randomPipaY,
    width: pipaLebar,
    height: pipaTinggi,
    passed: false,
  };
  pipaArray.push(pipaAtas);

  let pipaBawah = {
    img: botPipaImg,
    x: pipaX,
    y: randomPipaY + pipaTinggi + openSpace,
    width: pipaLebar,
    height: pipaTinggi,
    passed: false,
  };
  pipaArray.push(pipaBawah);
}

function gerakBurung(e) {
  if (e.code == "Space" || e.code == "ArrowUp") {
    velocityY = -6;

    if (gameOver) {
      avatar.y = avatarY;
      velocityY = 0;
      pipaArray = [];
      score = 0;
      gameOver = false;
    }
  }
}

function detectCollision(a, b) {
  let margin = 5;
  return (
    a.x + margin < b.x + b.width - margin &&
    a.x + a.width - margin > b.x + margin &&
    a.y + margin < b.y + b.height - margin &&
    a.y + a.height - margin > b.y + margin
  );
}
