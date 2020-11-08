Tic Tac Toe game project, The Odin Project: https://www.theodinproject.com/courses/ruby-programming/lessons/tic-tac-toe)

Technique.
Origin technique: https://www.vikingcodeschool.com/professional-development-with-ruby/tic-tac-toe
Step 1. Pseudocode v1.
        Set up the game initially
            Create a game board
            Create a couple players
        Start the game loop
            Render the board
            Ask for and validate the current player's coordinates
            If the game should end
                Display the proper victory or draw message
                Stop looping
            Else
                Switch to the next player and keep looping
Step 2. Pseudocode v2 - defining classes.
        Set up the game initially [class TicTacToe]
            Create a game board [class Board]
            Create a couple players [class Player]
        Start the game loop [class TicTacToe]
            Render the board [class Board]
            Ask for and validate the current player's coordinates [class Player]
            If the game should end [class TicTacToe]
                Display the proper victory or draw message
                Stop looping
            Else
                Switch to the next player and keep looping [class TicTacToe]
        => there are 3 classes: TicTacToe (controls the overall game), Board (preserves the board state and renders it) and Player (models each player).
Step 3. Pseudocode to Ruby code
    #1) Controls the game play
        class TicTacToe
            #initialize
            def initialize
                #set up the board
                @board = Board.new
                #set up the players
                @player_x = Player.new("X-player", :x, @board)
                @player_o = Player.new("O-player", :o, @board)
                #assign the starting player
                @current_player = @player_x
            #play
            def play
                #loop infinitely
                loop do
                    #call the board rendering method
                    @board.render
                    #ask for coordinates from the current player
                    @current_player.get_coordinates
                    #break the loop if the game is over
                    break if check_game_over
                    #switch players
                    switch_players
                end
            end
            #check_game_over
            def check_game_over
                #check_victory
                #check_draw
                check_victory || check_draw
            end
            #check_victory?
            def check_victory
                #if Board says current player's piece has
                #a winning_combination?
                if @board.winning_combination?(@current_player.piece)
                    #display a victory message
                    puts "Congratulations #{@current_player.name}, you win!"
                    true
                else
                    false
                end
            end
            #check_draw?
            def check_draw
                #if Board says we have filled up
                if @board.full?
                    #display a draw message
                    puts "Bummer, you've drawn ..."
                    true
                else
                    false
                end
            end
            #switch_players
            def switch_players
                #PlayerX >> PlayerO or vice versa
                if @current_player == @player_x
                    @current_player = @player_o
                else
                    @current_player = @player_x
                end
            end
        end
    #2) Manages all player-related functionality
        class Player
            attr_accessor :name, :piece
            #initialize
            def initialize(name = "Player", piece, board)
                #Set marker type (e.g. X or O)
                raise "Piece must be a Symbol!" unless piece.is_a?(Symbol)
                @name = name
                @piece = piece
                @board = board
            end
            #get_coordinates
            def get_coordinates
                #loop infinitely
                loop do
                    #ask_for_coordinates
                    coords = ask_for_coordinates
                    #if validate_coordinates_format is true
                    if validate_coordinates_format(coords)
                        #if piece can be placed on Board
                        if @board.add_piece(coords, @piece)
                            #break the loop
                            break
                        end
                    end
                end
            end
            #ask_for_coordinates
            def ask_for_coordinates
                #Display message asking for coordinates
                puts "#{@name} (#{piece}), enter enter your coordinates in the form x,y:""
                #pull coordinates from command line
                gets.strip.split(",").map(&:to_i)
            end
            #validate_coordinates_format
            def validate_coordinates_format(coords)
                #unless coordinates are in the proper format
                if coords.is_a?(Array) && coords.size == 2
                    true
                else
                    #display error message
                    puts "Your coordinates are in the improper format!"
                end
            end
        end
    #3) Maintains game board state
        class Board
            #initialize board
            def initialize
                #set up blank data structure
                @board = Array.new(3){Array.new(3)}
            end
            #render
            def render
                puts
                #loop through data structure
                @board.each do |row|
                    row.each do |cell|   
                        #display an existing marker if any, else blank
                        cell.nil? ? print("-") : print(cell.to_s)
                    end
                    puts
                end
                puts
            end
            #add_piece
            def add_piece(coords, piece)
                #if piece_location_valid?
                if piece_location_valid?(coords)
                    #place piece
                    @board[coords[0]][coords[1]] = piece
                    true
                else
                    false
                end
            end
            #piece_location_valid?
            def piece_location_valid?(coords)
                #is the placement within_valid_coordinates?
                if within_valid_coordinates?(coords)
                    #are the piece coordinates_available?
                    coordinates_available?(coords)
                end
            end
            #within_valid_coordinates?
            def within_valid_coordinates?(coords)
                #unless piece coordinates are in the acceptible range
                if (0..2).include?(coords[0]) && (0..2).include?(coords[1])
                    true
                else
                    #display error message
                    puts "Piece coordinates are out of bounds"
                end
            end
            #coordinates_available?
            def oordinates_available?(coords)
                #unless piece coordinates are not occupied
                if @board[coords[0]][coords[1]].nil?
                    true
                else
                    #display error message
                    puts "There is already a piece there!"
                end
            end
            #winning_combination?
            def winning_combination?(piece)
                #is there a winning_diagonal?
                #or winning_vertical?
                #or winning_horizontal? for that piece?
                winning_diagonal?(piece) || winning_horizontal?(piece) || winning_vertical?(piece)
            #winning_diagonal?
            def winning_diagonal?(piece)
                #check if specified piece has a triplet across diagonals
                diagonals.any? do |diag|
                    diag.all? { |cell| cell == piece }
                end
            end
            #winning_vertical?
            def winning_vertical?(piece)
                #check if specified piece has a triplet across verticals
                verticals.any? do |vert|
                    vert.all? { |cell| cell == piece }
                end
            end
            #winning_horizontal?
            def winning_horizontal?(piece)
                #check if specified piece has a triplet across horizontals
                horizontals.any? do |horz|
                    horz.all? { |cell| cell == piece }
                end
            end
            #diagonals
            def diagonals
                #return the diagonal pieces
                [[ @board[0][0], @board[1][1], @board[2][2] ],[ @board[2][0], @board[1][1], @board[0][2] ]]
            #verticals
            def verticals
                #return the vertical pieces
                @board
            end
            #horizontals
            def horizontals
                #return the horizontal pieces
                horizontals = []
                3.times do |i|
                    horizontals << [@board[0][i], @board[1][i], @board[2][i]]
                end
            end
            #full?
            def full?
                #does every square contain a piece?
                @board.all? do |row|
                    row.none?(&:nil?)
                end
            end
        end
        t = TicTacToe.new
        t.play
