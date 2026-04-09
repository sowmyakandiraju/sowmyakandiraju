START

INITIALIZE Flask Application
SET secret key
CREATE uploads directory

CONFIGURE MySQL database connection

LOAD machine learning models:
    - SVM model
    - Random Forest model
    - Model comparison data
LOAD CNN deep learning model

--------------------------------------------------
FUNCTION cnn_preprocess(audio_file):
    LOAD audio (mono, 22050 Hz, 5 sec)
    CONVERT to Mel Spectrogram
    CONVERT to decibel scale
    RESHAPE to CNN input format
    RETURN processed data
--------------------------------------------------

ROUTE "/" (Home Page):
    DISPLAY home page

--------------------------------------------------

ROUTE "/register":
    IF request is POST:
        GET name, email, password
        INSERT user into database
        REDIRECT to login page
    ELSE:
        DISPLAY register page

--------------------------------------------------

ROUTE "/login":
    IF request is POST:
        GET email, password
        FETCH user from database
        IF user exists:
            STORE user session
            REDIRECT to dashboard
        ELSE:
            DISPLAY invalid login
    ELSE:
        DISPLAY login page

--------------------------------------------------

ROUTE "/dashboard":
    IF user not logged in:
        REDIRECT to login
    DISPLAY dashboard

--------------------------------------------------

ROUTE "/analysis":
    IF user not logged in:
        REDIRECT to login

    IF request is POST:
        RECEIVE audio file
        IF file missing:
            REDIRECT back

        GENERATE unique file ID
        SAVE uploaded file

        CONVERT audio to WAV format
        STORE file path in session

        REDIRECT to result page

    DISPLAY analysis page

--------------------------------------------------

ROUTE "/result":
    IF user not logged in:
        REDIRECT to login

    GET audio path from session
    IF file not found:
        REDIRECT to analysis

    EXTRACT audio features

    IF feature extraction fails:
        REDIRECT to analysis

    // Classical ML Predictions
    GET probability from SVM model
    GET probability from Random Forest model

    // CNN Prediction
    PREPROCESS audio for CNN
    GET CNN probability

    // Ensemble Prediction
    COMPUTE final probability:
        final = (0.3 * SVM) + (0.3 * RF) + (0.4 * CNN)

    CALCULATE risk score (0–100)

    DETERMINE risk level:
        IF score < 30 → Low
        IF score < 70 → Moderate
        ELSE → High

    SAVE result in database

    STORE result in session history

    GENERATE recommendations based on risk level

    DISPLAY result page with:
        - Score
        - Risk level
        - Recommendations

--------------------------------------------------

ROUTE "/history":
    IF user not logged in:
        REDIRECT to login

    FETCH all past assessments from database
    DISPLAY history

--------------------------------------------------

ROUTE "/profile":
    IF user not logged in:
        REDIRECT to login
    DISPLAY user profile

--------------------------------------------------

ROUTE "/logout":
    CLEAR session
    REDIRECT to login

--------------------------------------------------

END
