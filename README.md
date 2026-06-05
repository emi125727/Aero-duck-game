# 🦆 Utya: Winter is Coming / Arcade Game
## Жанр
2D пиксельная аркада на уклонение (vertical dodge arcade)

## Игровой мир
Игра происходит в небе. 
По мере прохождения игрок пересекает разные зоны: 
лес, горы, озёра, облачные регионы, финальная южная локация (тёплые земли). Каждая зона отличается погодой, препятствиями и сложностью.
Утя летит впёред, а игрок управляет её движениями, уворачиваясь от препятствий. Управление происходит только влево или вправо.

## Сюжет
Утка по имени "Утя" летит осенью на юг вместе со стаей. По пути она сталкивается с разными природными препятствиями и другими птицами. Когда гг долетает до юга, её стая появляется на экране. Они радостно отмечают завершение миграции.
Цель - добраться до тёплых краёв, преодолев все трудности пути.

## Персонажи и враги
Главный герой - Утя, также позади неё будут лететь другие утки из ее стаи, но их не будет видно. Враги: другие птицы, природные явления.




using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Input;
using ProjectDuckgame.Core;
using static System.Net.Mime.MediaTypeNames;


namespace ProjectDuckgame //дизайн
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;
        SpriteBatch spriteBatch;

        GameWorld world;
        GameState currentState = GameState.MainMenu;
        int screenWidth;
        int screenHeight;

        Texture2D duckTex;

        Texture2D birdTex;
        Texture2D snowTex;
        Texture2D rainTex;
        Texture2D lightningTex;

        Texture2D forestBg;
        Texture2D mountainBg;
        Texture2D lakeBg;
        Texture2D cloudBg;
        Texture2D southBg;

        Rectangle playButton;
        Rectangle settingsButton;
        Rectangle exitMenuButton;
        Texture2D menuBackground;

        Texture2D playButtonTex;
        Texture2D settingsButtonTex;
        Texture2D exitButtonTex;

        bool isPaused = false;
        Rectangle pauseButton;
        Rectangle resumeButton;
        Rectangle exitButton;
        MouseState previousMouse;

        Texture2D pauseIcon;
        Texture2D whitePixel;
        SpriteFont font;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);

            // Включаем полноэкранный режим без рамок
            graphics.HardwareModeSwitch = false;
            graphics.IsFullScreen = true;


            // Автоматически подстраиваемся под родное разрешение монитора пользователя
            graphics.PreferredBackBufferWidth = GraphicsAdapter.DefaultAdapter.CurrentDisplayMode.Width;
            graphics.PreferredBackBufferHeight = GraphicsAdapter.DefaultAdapter.CurrentDisplayMode.Height;

            graphics.ApplyChanges();

            Content.RootDirectory = "Content";
            IsMouseVisible = true;
        }

     


        protected override void Initialize()
        {
            screenWidth = GraphicsDevice.Viewport.Width;
            screenHeight = GraphicsDevice.Viewport.Height;
            // TODO: Add your initialization logic here
            world = new GameWorld();
            base.Initialize();

            Rectangle playButton = new Rectangle(760, 350, 400, 100);
            Rectangle settingsButton = new Rectangle(760, 350, 400, 100);
            Rectangle exitButton = new Rectangle(760, 350, 400, 100);

            pauseButton = new Rectangle(
                graphics.PreferredBackBufferWidth - 84,
                20,
                64,
                64);

            resumeButton = new Rectangle(
                760,
                400,
                400,
                80);

            exitButton = new Rectangle(
                760,
                520,
                400,
                80);

        }

        protected override void LoadContent()
        {
            spriteBatch = new SpriteBatch(GraphicsDevice);

            duckTex = Content.Load<Texture2D>("duck");
            birdTex = Content.Load<Texture2D>("bird");
            snowTex = Content.Load<Texture2D>("snow");
            rainTex = Content.Load<Texture2D>("rain");
            lightningTex = Content.Load<Texture2D>("lightning");

            menuBackground = Content.Load<Texture2D>("menu");

            playButtonTex = Content.Load<Texture2D>("play");
            settingsButtonTex = Content.Load<Texture2D>("settings");
            exitButtonTex = Content.Load<Texture2D>("exit");

            pauseIcon = Content.Load<Texture2D>("pause");
            font = Content.Load<SpriteFont>("font");
            whitePixel = new Texture2D(GraphicsDevice, 1 ,1);
            whitePixel.SetData(new[] { Color.White });

            forestBg = Content.Load<Texture2D>("forest");
            mountainBg = Content.Load<Texture2D>("mountain");
            lakeBg = Content.Load<Texture2D>("lake");
            cloudBg = Content.Load<Texture2D>("cloud");
            southBg = Content.Load<Texture2D>("south");
            // TODO: use this.Content to load your game content here
        }

        protected override void Update(GameTime gameTime)
        {
            float dt = (float)gameTime.ElapsedGameTime.TotalSeconds;
            var kb = Keyboard.GetState();
            var mouse = Mouse.GetState();
            if (currentState == GameState.MainMenu)
            {
                if(mouse.LeftButton == ButtonState.Pressed && previousMouse.LeftButton == ButtonState.Released)
                {
                    if (playButton.Contains(mouse.Position)){
                        currentState = GameState.Playing;
                    }

                    if (settingsButton.Contains(mouse.Position))
                    {

                    }

                    if (exitMenuButton.Contains(mouse.Position))
                    {
                        Exit();
                    }
                }

                previousMouse = mouse;
                return;
            }

            if (mouse.LeftButton == ButtonState.Pressed && previousMouse.LeftButton == ButtonState.Released)
            {
                Point clickPoint = mouse.Position;
                if (pauseButton.Contains(clickPoint))
                {
                    isPaused = !isPaused;
                }

                if (isPaused)
                {
                    if (resumeButton.Contains(clickPoint))
                    {
                        isPaused = false;
                    }

                    if(exitButton.Contains(clickPoint))
                    {
                        Exit();
                    }
                }
            }

            previousMouse = mouse;

            //движение утки
            if (!isPaused)
            {
                if (kb.IsKeyDown(Keys.Left))
                    world.Player.MoveLeft();

                if (kb.IsKeyDown(Keys.Right))
                    world.Player.MoveRight();
            }

            world.Player.Clamp(
                0,
                graphics.PreferredBackBufferWidth - 120);

            // обнова мира
            if (!isPaused)
            {
                world.Update(dt);
            }
            


            // TODO: Add your update logic here
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);
            spriteBatch.Begin();

            DrawBackground();

            if(currentState == GameState.MainMenu)
            {
                GraphicsDevice.Clear(Color.Black);

                spriteBatch.Begin(samplerState: SamplerState.PointClamp);
                spriteBatch.Draw(
                    menuBackground,
                    new Rectangle(
                        0,
                        0,
                        graphics.PreferredBackBufferWidth,
                        graphics.PreferredBackBufferHeight),
                    Color.White);

                spriteBatch.Draw(
                    playButtonTex,
                    playButton,
                    Color.White);

                spriteBatch.Draw(
                    settingsButtonTex,
                    settingsButton,
                    Color.White);

                spriteBatch.Draw(
                    exitButtonTex,
                    exitMenuButton,
                    Color.White);

                spriteBatch.End();
                return;
            }

            spriteBatch.Draw(
                pauseIcon,
                pauseButton,
                Color.White
                );

            spriteBatch.Draw(
                duckTex,
                new Rectangle(
                    (int)world.Player.X,
                    (int)world.Player.Y,
                    120,
                    120),
                Color.White);

            foreach (var o in world.Obstacles)
            {
                Texture2D tex;
                Rectangle rect;

                switch (o.Type)
                {
                    case ObstacleType.Bird:
                        tex = birdTex;
                        rect = new Rectangle(
                            (int)o.X,
                            (int)o.Y,
                            260,
                            160);
                        break;

                    case ObstacleType.Snow:
                        tex = snowTex;
                        rect = new Rectangle(
                            (int)o.X,
                            (int)o.Y,
                            160,
                            160);
                        break;

                    case ObstacleType.Rain:
                        tex = rainTex;
                        rect = new Rectangle(
                            (int)o.X,
                            (int)o.Y,
                            320,
                            320);
                        break;

                    case ObstacleType.Lightning:
                        tex = lightningTex;
                        rect = new Rectangle(
                            (int)o.X,
                            (int)o.Y,
                            240,
                            160);
                        break;

                    default:
                        tex = birdTex;
                        rect = new Rectangle(
                            (int)o.X,
                            (int)o.Y,
                            240,
                            160);
                        break;
                }
                spriteBatch.Draw(tex, rect, Color.White);
            }

            if (isPaused)
            {
                spriteBatch.Draw( //затемнение экрана
                    whitePixel,
                    new Rectangle(
                        0,
                        0,
                        graphics.PreferredBackBufferWidth,
                        graphics.PreferredBackBufferHeight),
                    Color.Black * 0.6f);

                spriteBatch.Draw( //меню
                    whitePixel,
                    new Rectangle(
                        600,
                        250,
                        700,
                        450),
                    Color.DarkSlateGray);

                spriteBatch.Draw(
                    whitePixel,
                    resumeButton,
                    Color.Green);

                spriteBatch.Draw(
                    whitePixel,
                    exitButton,
                    Color.Red);

                spriteBatch.DrawString(
                    font,
                    "ПАУЗА",
                    new Vector2(860, 300),
                    Color.White);

                spriteBatch.DrawString(
                    font,
                    "ПРОДОЛЖИТЬ",
                    new Vector2(820, 425),
                    Color.Black);

                spriteBatch.DrawString(
                    font,
                    "ВЫЙТИ",
                    new Vector2(900, 545),
                    Color.White);


            }
            spriteBatch.End();
            base.Draw(gameTime);
            // TODO: Add your drawing code here         
        }

        void DrawBackground()
        {
            Texture2D bg =
                world.Zone == ZoneType.Forest ? forestBg :
                world.Zone == ZoneType.Mountains ? mountainBg :
                world.Zone == ZoneType.Lake ? lakeBg :
                world.Zone == ZoneType.Clouds ? cloudBg :
                southBg;

            spriteBatch.Draw(
                bg,
                new Rectangle(
                    0,
                    0,
                    graphics.PreferredBackBufferWidth,
                    graphics.PreferredBackBufferHeight),
                Color.White);
        }

    }
}
